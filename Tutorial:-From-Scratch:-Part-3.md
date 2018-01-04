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

```  
using UnityEngine;
using UnityEngine.Networking;

public class ScratchNetworkManager : NetworkManager
{
    public UnetGameRoom GameRoom;

    void Awake()
    {
        if (GameRoom == null)
        {
            Debug.LogError("Game Room property is not set on NetworkManager");
            return;
        }

        // Subscribe to events
        GameRoom.PlayerJoined += OnPlayerJoined;
        GameRoom.PlayerLeft += OnPlayerLeft;
    }

    /// <summary>
    /// Regular Unet method, which gets called when client disconnects from game server
    /// </summary>
    /// <param name="conn"></param>
    public override void OnServerDisconnect(NetworkConnection conn)
    {
        base.OnServerDisconnect(conn);

        // Don't forget to notify the room that a player disconnected
        GameRoom.ClientDisconnected(conn);
    }
    
    /// <summary>
    /// Invoked, when client provides a successful access token and enters the room
    /// </summary>
    /// <param name="player"></param>
    private void OnPlayerJoined(UnetMsfPlayer player)
    {
        Debug.Log("Player joined");
        // Create an instance of the player prefab we made earlier
        var playerObj = Instantiate(Resources.Load<ScratchPlayerController>("PlayerPrefab"));

        //find all spawn points, we just have the one, but you can add more if you like
        var spawns = FindObjectsOfType<NetworkStartPosition>();
        var spawn = spawns[Random.Range(0, spawns.Length)];
        playerObj.transform.position = spawn.transform.position;

        //add the connection to the game server to make sure the player can update on the client and the gameserver
        NetworkServer.AddPlayerForConnection(player.Connection, playerObj.gameObject, 0);
        
        return;
    }
    

    private void OnPlayerLeft(UnetMsfPlayer player)
    {

    }

    public void DisconnectAllPlayers()
    {
        foreach (var player in FindObjectsOfType<ScratchPlayerController>())
        {
            player.connectionToClient.Disconnect();
        }
    }
}  
```

The following scripts are fairly generic so they can be used for many different kinds of games. We will (probably) be covering how to make custom versions in their own tutorial later. (If there is a tutorial, please link it here or contact "Trivn" in the discord)

Make a GameObject called *UnetGameRoom* and add the included script *UnetGameRoom.cs*.

Make a GameObject called *RoomTerminator* and add the included script *UnetGameTerminator.cs*.

Make a GameObject called *UnetServerStarter* and add the included script *UnetServerStarter.cs*.

Make a GameObject called *UnetConnector* and add the included script *UnetRoomConnector.cs*.

And that wraps up the level!

# Step 3

Switch back to **scratchClient** and add a GameObject called *UnetConnector* and add the script *UnetRoomConnector*. This is the same script we added to the final component in the last scene. The client needs this to connect to the GameServer, and the game instance needs this to connect itself to the game room. It's similar to how the MasterServer scene needs a "ConnectionToMaster" object even though the server object is in the same scene. 

Now for the call to request a game instance be made:

### ScratchCreateGame

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
            //LOG IN AS GUEST
            //This is part of the error in judgment. In order for the serverside code to work
            //and the gameServer to connect to the client properly, it needs to be authenticated. 
            //To get around this, we're going to trigger the "Guest access" by default before we 
            //spawn the gameserver.
            //I will provide a tutorial at a later date to explain how the authModule works and how
            //to write your own.
                var promise = Msf.Events.FireWithPromise(Msf.EventNames.ShowLoading, "Logging in");
                Msf.Client.Auth.LogInAsGuest((accInfo, error) =>
                {
                    promise.Finish();

                    if (accInfo == null)
                    {
                        Msf.Events.Fire(Msf.EventNames.ShowDialogBox, DialogBoxData.CreateError(error));
                        Logs.Error(error);
                    }
                });
            

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

Now add a button to the **scratchClient** scene with the above script attached to it. Add an *OnClick* event on the button pointing to the script and set it to "ScratchCreateGame.OnCreateClick".

For the Map variable on the script, set the name to scratchLevel, and drag the **scratchLevel** scene to the Scene variable.

---

## Step 4

Last but not least, we need a player object for the game to spawn in! If you look at line 41 in *ScratchNetworkManager* you'll see that we Instantiated a game object called "PlayerPrefab". We're going to make that now.

First, create a folder named "Resources". If you don't know how resources and instantiation from resources works, please look up an explanation on that if you don't understand how this works.

Inside the scene (doesn't matter which at the moment) create a cube. Set its position and orientation to 0,0,0. Add a **Network Identity** component and check off "local Player Authority".

Next, add a new script called *ScratchPlayerController*. The code is simple, so it will not be explained in depth:

```
using UnityEngine;
using UnityEngine.Networking;

public class ScratchPlayerController : NetworkBehaviour
{
    
    private readonly float _forwardMaxSpeed = 5f;
    private readonly float _rotationMaxVelocity = 270;

    private float _forwardSpeed;
    private float _rotationVelocity;
    
    private void Update()
    {
        // Ignore input from other players
        if (!isLocalPlayer)
            return;

        _forwardSpeed = Input.GetAxis("Vertical") * _forwardMaxSpeed;
        _rotationVelocity = Input.GetAxis("Horizontal") * _rotationMaxVelocity;
        
        UpdateMovement();
    }

    private void UpdateMovement()
    {
        var moveDirection = transform.forward * _forwardSpeed * Time.deltaTime;
        
        transform.position += moveDirection;

        transform.Rotate(Vector3.up * _rotationVelocity * Time.deltaTime);
    }

    public void MoveToRandomSpawnPoint()
    {
        var spawns = FindObjectsOfType<NetworkStartPosition>();
        var spawn = spawns[Random.Range(0, spawns.Length)];
        transform.position = spawn.transform.position;
    }
}
```

Name the object "PlayerPrefab" and make it a prefab and put it in the *Resources* folder created earlier. Delete the object from the scene.

---
# (Update from lazalong)

If you followed exactly the steps... well it will not quite work. 
Here are some additional steps you will need to do:

In MasterAndSpawner scene:
   - Set the spawner 'default exe path' field to the location of your scratchGame.exe (i.e. './scratchGame.exe') - not only the path.

In Level scene:
   - Add the PlayerPrefab to the fields SpawnInfo 'PlayerPrefab' and 'Registred Spawnable Prefabs' of the ScratchNetworkManager script in the NetworkManager object.
   - Reference the UnetGameRoom object in the SpawnInfo.GameRoom field of the NetworkManager script.
   - Do not check the field AutoCreatePlayer in the SpawnInfo of the ScratchNetworkManager script in the NetworkManager object (or there will be an instance on the gameserver that isn't seen on the client)
   - The UnetGameRoom object with the UnetGameTerminator script and set its Room field to the UnetGameRoom gameobject (without it the room will not self-destruct when the client quit).
   - (useful? set in UnetGameRoom object the Network Identity.SeverOnly field to true )
   - (nice to have) in UnetRoomConnector set the fields ConnectionFailedScene and Disconnected Scene to the Client scene

In the Client scene:
   - In the script scratchCreateGame script attached to the start game button, set the field Map.Scene to the scene scratchLevel 

In the GameServer scene:
   - Set the field ConnectionToMaster.ConnectOnStart to checked. 
   - Add the script QuickGameServerScene to a game object

In PlayerPrefab:
   - Add a NetworkTransform script (and click on the NetworkTransform.SyncAngularVelocity field)
   - Set on the NetworkIdentity.LocalPlayerAuthority field

(end update)
---

And that's it! With this, you can build *scratchGame* and *scratchLevel* together, build *scratchMaster* and start it up, then run *scratchClient* in the editor.

The end result should be a cube on the screen that can be moved forwards and backward and rotated.

(If anything doesn't work, please contact Trivn in the discord server.)

# DOWNLOAD THE PROJECT

Convenient link to download the project and check to see if everything matches if your project isn't working.  
[Download the project here!](https://drive.google.com/open?id=0B_t7He3NQzRbMHRZd1o1YmJ5dm8)

If anything in this link doesn't work, please contact Trivn in the discord server.  
If anything in this link is needed for the project and was left out in the tutorial, please contact Trivn in the discord server.  
If you have issues in general, please contact Trivn in the discord server.  