## Master Server Side

### 1. Add Games Module

* Create an empty game object as a child of **Master Server**
* Name it **GamesModule**
* Attach "GamesModule" component

#### ℹ️ What Is The "Games Module"?

It's a module, responsible for handling connections from game servers. At it's core, this module **opens a port** (5001 by default), and game servers use this port to connect to master server.

After connecting to master server, game server identifies itself, so that master server knows about available game servers.

### 2. Add Lobbies Module

🌶 We're adding this because Matchmaker depends on it

* Create an empty game object as a child of **Master Server**
* Name it **LobbiesModule**
* Attach "LobbiesModule" component

### 3. Add Matchmaker Module

* Create an empty game object as a child of **Master Server**
* Name it **MatchmakerModule**
* Attach "MatchmakerModule" component

ℹ️ **Matchmaker module** handles requests from clients, and returns a list of available games

### 4. Add Spawners Module

🌶 We're adding this because Lobbies module depends on it

* Create an empty game object as a child of **Master Server**
* Name it **SpawnersModule**
* Attach "SpawnersModule" component

![](http://i.imgur.com/OhJYWeR.png)

## Client Side

### 1. Add a button to retrieve available games

* Create button in the scene, and name the object **GetGames**

![](http://i.imgur.com/vmCBktB.png)

### 2. Create a new script and name it **GamesRequester**.

* Attach the script to **GetGames** object (button)
* Copy the code to the **GamesRequester.cs**

``` C#
using System.Collections.Generic;
using System.Linq;
using Barebones.MasterServer;
using Barebones.Networking;
using UnityEngine;
using UnityEngine.UI;

public class GamesRequester : MonoBehaviour {

    void Awake()
    {
        var button = GetComponent<Button>();
        button.onClick.AddListener(RequestGames);
    }

    private void RequestGames()
    {
        if (!Connections.ClientToMaster.IsConnected)
        {
            Debug.LogError("You're not connected to master server");
            return;
        }

        Debug.Log("Requesting a list of games");

        // Create an empty message
        var msg = MessageHelper.Create(BmOpCodes.GamesListRequest);
        
        // Send the request
        Connections.ClientToMaster.SendMessage(msg, (status, response) =>
        {
            if (status != AckResponseStatus.Success)
            {
                // If server responds with something other than success
                Debug.LogError("Server did not handle the request properly");
                return;
            }

            // Deserialize received data into a list
            var data = response.DeserializeList(() => new GameInfoPacket()).ToList();

            OnGamesListReceived(data);
        });
    }

    public void OnGamesListReceived(List<GameInfoPacket> data)
    {
        Debug.Log("Found games: " + data.Count);

        if (data.Count > 0)
        {
            var games = data.Select(g => string.Format("Name: {0}, Address: {1} \n", g.Name, g.Address));
            Debug.Log(string.Join(", ", games.ToArray()));
        }
    }
}

```

### :fire: (Optional) "Games List" component

> Framework contains a prefab that retrieves a list of available games. You can find it at `Barebones/MasterModules/Games/Prefabs/GamesList`. You can use it, extend it or create your own

## Testing If It Works

* Start The Master Server
* Start The Client
* Hit the "Get Games" button

You should see something like this in the logs:

![](http://i.imgur.com/7TgiS3J.png)

The number of games should be 0, because we have not registered any game servers to the master server