This tutorial will focus on showing you how master server finds out about existing game servers, and how they appear on the "games list".

To make it easier to understand, we will first try to register a "fake" game server, because this way it's easier to see how the whole process works in general, and how you can **register any kind of game server**.

## How Game Servers Are Registered?

When game server starts, it should "register" to master server, so that master server knows about it's existence, and can inform players about it (via "games list" view or any other way).

In essence, to register a game server, you'd need to call `GamesModule.RegisterGame()`, and it will have the game appear in the "games list". In fact, you don't even need a real game server to register it to the list - it can be just a fake / mock / empty object, which implements `IGameServer` interface.

So `GamesModule.RegisterGame()`takes these parameters:

* `RegisterGameServerPacket` - information about the game server 
* `IGameServer` implementation - I'm not sure if generic implementation will work for you (), but you can see how specific methods are implemented, and do something similar
* GameServerRegisterCallback - callback, which will be called when the game server is registered to master server. This callback receives RegisteredGame object, which is sort of a link between master server and game server. You'll need to call `registeredGame.Open()` to make the game visible in the Games List

⚠️ _In V1.03.2, an overload method for `GamesModule.RegisterGame()` was introduced, and it no longer requires `RegisterGameServerPacket` parameter_

## Creating A Fake Game Server

Creating a fake game server should give you an idea on how to use the API to register any type of game server you might have - whether it's made with uNet, Forge, or anything else.

⚠️ Everything will be done on the "**Client**" project

### 1. Create an empty game object as a child of `Canvas`

### 2. Create a new script and call it `FakeGameServer`

### 3. Attach the newly created script to the FakeGameServer object

### 4. Add a UI button as a child of FakeGameServer, name it "StartAndRegister"

### 5. Add a UI button as a child of FakeGameServer, name it "StopAndDisconnect"

![](http://i.imgur.com/tChr9Qs.png)

* Start And Register - will start our fake game server, and register it to the master
* Stop And Disconnect - will stop the game server and disconnect it from master

### 6. Writing the code for game server

:warning: There's a bug, `WaitConnection` is invoked multiple times after reconnecting. It's fixed in V1.03.2
:warning: _In V1.03.2, an overload method for `GamesModule.RegisterGame()` was introduced, and it no longer requires `RegisterGameServerPacket` parameter_

Study the comments in the code.

``` C#
using System.Collections.Generic;
using Barebones.MasterServer;
using Barebones.Networking;
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

/// <summary>
/// Our game server needs to implement <see cref="IGameServer"/>
/// </summary>
public class FakeGameServer : MonoBehaviour, IGameServer
{
    public Button StartButton;
    public Button StopButton;

    public string MasterKey = "";
    public string MasterIp = "127.0.0.1";
    public int MasterPort = 5001;

	// Use this for initialization
	void Awake () {
	    StartButton.onClick.AddListener(OnStartClick);	
	    StopButton.onClick.AddListener(OnStopClick);

        StopButton.gameObject.SetActive(false);
	    Application.runInBackground = true;
	}

    private void OnStartClick()
    {
        // Connect to master server
        Debug.Log("---------------------");
        Debug.Log("Connecting to master server");

        // Connect to master
        if (!Connections.GameToMaster.IsConnected)
        {
            Connections.GameToMaster.Connect(MasterIp, MasterPort, 10000);
        }

        StartButton.interactable = false;

        Connections.GameToMaster.WaitConnection(socket =>
        {
            if (!socket.IsConnected)
            {
                Debug.LogError("Failed to connect to master server");
                return;
            }

            Debug.Log("Connected to master");
            Debug.Log("Sending registration request");

            GamesModule.RegisterGame(this.CreateRegisterPacket(MasterKey), this, game =>
            {
                if (game == null)
                {
                    Debug.LogError("Failed to register game server");
                    return;
                }

                // Edit button visibility
                StartButton.gameObject.SetActive(false);
                StopButton.gameObject.SetActive(true);

                // Open the game for public access
                game.Open();
            });
        });
    }

    private void OnStopClick()
    {
        // Unregister the game
        GamesModule.CurrentGame.Unregister();

        // Disconnect from master
        Connections.GameToMaster.Disconnect();

        // Update button visibility
        StopButton.gameObject.SetActive(false);
        StartButton.gameObject.SetActive(true);
        StartButton.interactable = true;
    }

    #region IGameServer implementation

    /// <summary>
    /// This method should create a registration packet, which will be sent
    /// to master server. It gives master server some information about itself
    /// </summary>
    /// <param name="masterKey"></param>
    /// <returns></returns>
    public RegisterGameServerPacket CreateRegisterPacket(string masterKey)
    {
        // Create the packet
        var packet =  new RegisterGameServerPacket()
        {
            Name = "Fake game room",
            MaxPlayers = 10,
            PublicAddress = "xxx.xxx.xxx.xxx:xxxx",
            Properties = new Dictionary<string, string>()
            // There are more fields, but they are not necessary
        };

        packet.Properties.Add(GameProperty.MapName, "Best Map");

        return packet;
    }

    /// <summary>
    /// This should force-disconnect user by its name
    /// </summary>
    /// <param name="username"></param>
    public void DisconnectPlayer(string username)
    {
    }

    /// <summary>
    /// This should return a scene of the game server,
    /// it can be used when client wants to go to a specific scene before
    /// joining a game server
    /// </summary>
    /// <returns></returns>
    public string GetClientConnectionScene()
    {
        return SceneManager.GetActiveScene().name;
    }

    /// <summary>
    /// Called when server registers to master server
    /// </summary>
    /// <param name="masterConnection"></param>
    /// <param name="game"></param>
    public void OnRegisteredToMaster(IClientSocket masterConnection, RegisteredGame game)
    {
        Debug.Log("Server registered to master");
    }

    /// <summary>
    /// Called, when game server is opened and can be accesses by players
    /// </summary>
    public void OnOpenedToPublic()
    {
        Debug.Log("Game server is Open for public access");
    }

    /// <summary>
    /// Should force-shutdown the server (for example, when master server decides
    /// that this game server should no longer be running)
    /// </summary>
    public void Shutdown()
    {
        Application.Quit();
    }

    /// <summary>
    /// Checks if access should be given to the requester.
    /// Returns false, if access should not be given.
    /// This is a good place to implement custom logic for player bans and etc.
    /// </summary>
    public bool ValidateAccessRequest(GameAccessRequestPacket request, out string error)
    {
        error = null;
        return true;
    }

    /// <summary>
    /// This is called right before game server gives access data to a player.
    /// You can use this method to add custom properties to access data.
    /// </summary>
    public void FillAccessProperties(GameAccessRequestPacket request, GameAccessPacket accessData)
    {
    }

    #endregion
}
```

### 7. Add buttons references to the script in the inspector

![](http://i.imgur.com/nqGXDVR.png)

## Testing If It Works

* Start the master server
* Start the client
* Click on the "Start And Register" button
* Click on the "Get Games" button

You should see something similar. 

![] (http://i.imgur.com/XLpLIA4.png)

If you have the "Games List" component in your scene, it should also contain our new fake game server

![](http://i.imgur.com/kjUMYLk.png)