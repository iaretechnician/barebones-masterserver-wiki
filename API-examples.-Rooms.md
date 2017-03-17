## Creating a Room

### Minimal Example

``` C#
// This will register a room to the master server, so that
// master server would know about it's existance
// This is a minimal example
Msf.Server.Rooms.RegisterRoom((controller, error) =>
{
    if (controller == null)
        Logs.Error(error);
});
```

### Customized Example

``` C#
var roomOptions = new RoomOptions()
{
    IsPublic = false,
    MaxPlayers = 5,
    Name = "My super room",
    Password = "pssw",
    Properties = new Dictionary<string, string>()
    {
        {"CustomProperty", "Some extra stuff" }
    },
    RoomIp = "127.0.0.1",
    RoomPort = 777
};

// More customization options
Msf.Server.Rooms.RegisterRoom(roomOptions, (controller, error) =>
{
    // Edit the options, to make the room public
    controller.Options.IsPublic = true;

    // Save the options
    controller.SaveOptions(controller.Options);

    // When player sends us an access token, we can confirm if the token is valid
    controller.ValidateAccess("token..", (playerPeerId, confirmationError) =>
    {
        if (playerPeerId == null)
        {
            Logs.Error("Player provided an invalid token");
            return;
        }
                    
        // Player provided a valid token
        // TODO Get account info by peer id
        // TODO Spawn a player to the game
    });

    // If we want to handle who gets access, and who doesn't
    controller.SetAccessProvider((requester, giveAccess) =>
    {
        // TODO use the peerId to retrieve account data
        // TODO check if, for example, the username is banned in this room
                
        // If user is allowed, create a new room access
        giveAccess(new RoomAccessPacket()
        {
            RoomIp = controller.Options.RoomIp,
            RoomPort = controller.Options.RoomPort,
            Properties = new Dictionary<string, string>()
            {
                // Custom properties
                {"Color", "#ffffff" }
            },
            RoomId = controller.RoomId,
            SceneName = SceneManager.GetActiveScene().name,
            Token = Guid.NewGuid().ToString()
        }, null);

        // If user is not allowed
        giveAccess(null, "You're not allowed to play!");
    });
});
```

## Getting an Access To Room From Client

``` C#
var roomId = 5;

// Getting access from client
Msf.Client.Rooms.GetAccess(roomId, (access, error) =>
{
    if (access == null)
    {
        Debug.LogError(error);
        return;
    }

    // We've received the access
    Debug.Log(access);

    // TODO use ip and port from access to connect to game server
    // TODO send the token to game server  (this will depend on game server technologies used)
});
```

## 'Access Received' Event Handling

Every time a client receives an access to room, `Msf.Client.Rooms.AccessReceived` will be invoked. You can use it to automate connection to room. 

Here's an example of a script, which subscribes to the event, and tries to connect to game server using `NetworkManager`

⚠️ This is optional. You can use the access however you want right after requesting it. There might be no need for you to use this event

``` C#
using System;
using System.Collections;
using Barebones.MasterServer;
using UnityEngine;
using UnityEngine.Networking;
using UnityEngine.Networking.NetworkSystem;

public class RoomAccessExample : MonoBehaviour
{
    private Coroutine WaitConnectionCoroutine;

    void Awake()
    {
        // Subscribe to the event
        Msf.Client.Rooms.AccessReceived += OnAccessReceived;
    }

    private void OnAccessReceived(RoomAccessPacket access)
    {
        // This will be called when you receive an access

        if (access.Properties.ContainsKey(MsfDictKeys.SceneName))
        {
            // In case we received a name of the scene in the access
            // TODO switch scenes if necessary
            Debug.LogError("We might need to change scenes");
            return;
        }

        // We can use it to connect to Unet game server (this is just an example)
        var networkManager = FindObjectOfType<NetworkManager>();
        networkManager.networkAddress = access.RoomIp;
        networkManager.networkPort = access.RoomPort;

        // Start connecting
        networkManager.StartClient();

        if (WaitConnectionCoroutine != null)
            StopCoroutine(WaitConnectionCoroutine);

        // Wait until connected to server
        WaitConnectionCoroutine = StartCoroutine(WaitForConnection(() =>
        {
            // Client connected to server
            var tokenMsg = new StringMessage(access.Token);

            // Send the token to unet server
            networkManager.client.connection.Send(777, tokenMsg);
        }));
    }

    public IEnumerator WaitForConnection(Action callback)
    {
        var networkManager = FindObjectOfType<NetworkManager>();

        // This will keep skipping frames until client connects
        while (!networkManager.IsClientConnected())
            yield return null;

        callback.Invoke();
    }

    void OnDestroy()
    {
        // Unsubscribe from event
        Msf.Client.Rooms.AccessReceived -= OnAccessReceived;
    }
}

```