Continuing from [Tutorial: From Scratch: Part 2](https://github.com/alvyxaz/barebones-masterserver/wiki/Tutorial:-From-Scratch:-Part-2)
In Part 2, we set up some displays to indicate what was going on in the background. Now we're going to actually set up the Game Server and have something for the client to do.

We're going to be adding a bit of code in this tutorial. Some will be new, some will be taken from the included assets. We're not going to be walking through this line-by-line, but I'll add comments to explain what different parts are doing. I recommend reading through the original scripts as well to better understand what is going on, as well as what is possible to add.

### PLEASE READ:  
In my attempts to simplify everything and keep everything as tidy as possible, I made a small error in judgment. If we were truly to do this from scratch, we'd need to re-write several modules that allow the game to be played. I plan on covering these modules and how to write your own in later tutorials. For now, we'll make do with copying the modules from the **MasterServer** object from the scene Assets>Barebones>Demos>Quicksetup>Scenes>*MasterAndSpawner* and use it in our **scratchMasterServer** scene.

At the end of this tutorial, I will post a link to a download of the project thus far to ensure that any confusion is cleared up.

----

# Step 1

Make a new scene called **scratchGame** and delete everything inside it. We don't need to see what's happening, as this is running on the server where no one will look.  
This isn't going to be the game itself but a scene that is spawned by the Spawner and will run the server side of the game scene.

Next, throw in a *ConnectionToMaster* prefab. Now it'll find the MasterServer, but it still doesn't have anything to show game-wise. So let's make a game.

# Step 2

Make another new scene called **scratchLevel**. This is going to be a simple game as a proof of concept, but after we're done here, feel free to make it as complex as you want.

First, set the *Main Camera* and *Directional Light* Transforms as shown:
![](http://i.imgur.com/p5lbxvR.png)
![](http://i.imgur.com/PsPsVwu.png)

Add *ConnectionToMaster* object.

Add a GameObject called *StartPosition* and add the component *NetworkStartPosition*. Change its position and rotation to "0,0,0". (This is part of Unity's networking, not MSF)

### Network Manager

Make a GameObject called *NetworkManager* with a new script called *ScratchNetworkManager.cs*.

This is just a trimmed down version of the *NetworkManagerSample* included in the BareBones assets.

`

`

The following scripts are fairly generic so they can be used for many different kinds of games. We will (probably) be covering how to make custom versions in their own tutorial later. (If there is a tutorial, please link it here or contact "Trivn" in the discord)

Make a GameObject called *UnetGameRoom* and add the included script *UnetGameRoom.cs*.

Make a GameObject called *RoomTerminator* and add the included script *UnetGameTerminator.cs*.

Make a GameObject called *UnetServerStarter* and add the included script *UnetServerStarter.cs*.

Make a GameObject called *UnetConnector* and add the included script *UnetRoomConnector.cs*.

And that wraps up the level!

# Step 3

Switch back to **scratchClient** and add a GameObject called *UnetConnector* and add the script *UnetRoomConnector*. This is the same script we added to the final component in the last scene. The client needs this to connect to the GameServer, and the game instance needs this to connect itself to the game room. It's similar to how the MasterServer scene needs a "ConnectionToMaster" object even though the server object is in the same scene. 

Now for the call to request a game instance be made:

```
using System;
using System.Collections.Generic;
using UnityEngine;

namespace Barebones.MasterServer
{

    /// <summary>
    ///     Game creation window
    /// </summary>
    public class ScratchCreateGame : MonoBehaviour
    {
        //Allows the map name and scene to be set in the Inspector as a single object.
        //This is useful if you want to have multiple scenes available to choose from,
        //although, we will only have the one. 
        [Serializable]
        public class MapSelection
        {
            public string Name;
            public SceneField Scene;
        }

        public MapSelection map;

        //this will hold the reference to the request for the game instance to spawn
        SpawnRequestController Request;

        //This will be called when we click the button that this script will be attached to
        public void OnCreateClick()
        {
            //There can be more or fewer settings included, we're going to keep this simple.
            var settings = new Dictionary<string, string>
            {
                {MsfDictKeys.MaxPlayers, "20"},
                {MsfDictKeys.RoomName, map.Name},
                {MsfDictKeys.MapName, map.Name},
                {MsfDictKeys.SceneName, map.Scene.SceneName}
            };

            //The actual request message sent to the Spawner.
            //This returns a callback containing the SpawnRequestController that will allow us to track 
            //the progress of the spawn request
            Msf.Client.Spawners.RequestSpawn(settings, "", (requestController, errorMsg) =>
            {
                //If something went wrong, the request will return "null" and an error will be included
                if (requestController == null)
                {
                    Msf.Events.Fire(Msf.EventNames.ShowDialogBox,
                        DialogBoxData.CreateError("Failed to create a game: " + errorMsg));

                    Logs.Error("Failed to create a game: " + errorMsg);
                }

                //If the Controller is not null, we can track its progress
                TrackRequest(requestController);
            });
        }

        //Set up the Request variable to run OnStatusChange() when the status of our spawn request is changed
        public void TrackRequest(SpawnRequestController request)
        {
            if (Request != null)
                Request.StatusChanged -= OnStatusChange;

            if (request == null)
                return;

            request.StatusChanged += OnStatusChange;

            Request = request;
            gameObject.SetActive(true);

            //Typically we'd include an "abort" option, but in the spirit of keeping things simple,
            //we're leaving it out for this tutorial
        }

        protected void OnStatusChange(SpawnStatus status)
        {
            //We'll display the current status of the request
            Debug.Log(string.Format("Progress: {0}/{1} ({2})", (int)Request.Status, (int)SpawnStatus.Finalized, Request.Status));

            //If game was aborted
            if (status < SpawnStatus.None)
            {
                Debug.Log("Game creation aborted");
                return;
            }

            //Once the SpawnStatus reaches the Finalized state we can get the data from the finished request
            if (status == SpawnStatus.Finalized)
            {
                Request.GetFinalizationData((data, error) =>
                {
                    if (data == null)
                    {
                        Msf.Events.Fire(Msf.EventNames.ShowDialogBox,
                            DialogBoxData.CreateInfo("Failed to retrieve completion data: " + error));

                        Logs.Error("Failed to retrieve completion data: " + error);

                        Request.Abort();
                        return;
                    }

                    // Completion data received
                    OnFinalizationDataRetrieved(data);
                });
            }
        }

        public void OnFinalizationDataRetrieved(Dictionary<string, string> data)
        {
            //This comes from the "CreateGameProgressUi.cs" script. I'm not sure what could cause 
            //this error at this time, but it feels safer to leave it.
            if (!data.ContainsKey(MsfDictKeys.RoomId))
            {
                throw new Exception("Game server finalized, but didn't include room id");
            }

            var roomId = int.Parse(data[MsfDictKeys.RoomId]);

            //The request has finished, the game instance is made. Now we need to actually get access to the room.
            Msf.Client.Rooms.GetAccess(roomId, (access, error) =>
            {
                if (access == null)
                {
                    Msf.Events.Fire(Msf.EventNames.ShowDialogBox,
                            DialogBoxData.CreateInfo("Failed to get access to room: " + error));

                    Logs.Error("Failed to get access to room: " + error);

                    return;
                }
            });

            // Quick runthrough of what "GetAccess" does, as I found it complicated to trace:
            // The method is found in "MsfRoomsClient.cs". After checking that the Client is connected to the server,
            //     the request is forwarded to "RoomsModule.cs".
            // The RoomsModule checks to make sure that the requested room exists, then sends a request to the room for access.
            // This request is handled by "RegisteredRoom.cs". This script checks to make sure that this is not a duplicate request,
            //     that there isn't an unclaimed request token, and that the room isn't full.
            // If all the checks are passed, a message is sent that is handled by "RoomController.cs". This method obtains the 
            //     RoomController in the game instance. The accessProvider in that Controller is invoked and returns the RoomAccessPacket.
            // The RoomController responds to the RegisteredRoom which invokes the callback to the RoomsModule which responds to the 
            //     MsfRoomsClient. (Basically, everything falls back to the start)
            // The MsfRoomsClient then invokes the RoomConnector with the access packet. In this example, the connector instance is 
            //     handled by UnetRoomConnector.cs. This script changes to the proper scene if needed, then waits for the connection.
            // At this point, Logger.Info("Connected to game server, about to send access"); will be called. You can see this in the 
            //     console if the logging level is Info or lower.
            // The request is then sent to UnetGameRoom.cs, which validates the access request on the serverside, gets the account
            //     information, and finally calls "OnPlayerJoined()" which invokes "PlayerJoined", which will result in a call back 
            //     in our "ScratchNetworkManager" script. (This would normally end up in "NetworkManagerSample.cs" for the "quicksetup" demo

            // That's the quick version. For a quicker version (tl:dr):

            //     The access request is sent out, validated by several scripts on the client side, the client is switched to the
            //     proper scene, the access request is sent to the server, re-validated, then the final call for the player joining is 
            //     called in the game instance.
            // 
        }

        private void ShowError(string error)
        {
            Msf.Events.Fire(Msf.EventNames.ShowDialogBox, DialogBoxData.CreateError(error));
        }
    }
}
```