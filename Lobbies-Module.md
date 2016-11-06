⚠️ 🔴  If you're here, you probably got here ahead of time - the lobbies module is not yet released!

## Module Info

* Main script: `LobbiesModule`
* Dependencies: **SpawnersModule** and **GamesModule**

## What Is The Lobbies Module

This module introduces a concept of lobbies into your game. In games, lobbies act as a "gateway" to the game, they allow multiple players to gather before starting the actual game, and allows them to choose different options / settings and etc.

## Basic Principles

### Structure of The Lobby

![](http://i.imgur.com/5wcN6H6.png)

To make lobbies usable in many types of games, it was decided to represent lobby as a "**state machine of properties**". 

Basically, lobby consists of 3 entities:

1. Lobby itself
 2. Teams within the lobby
   3. Members within teams

Each one of these entities has a `Dictionary<string, string>` of properties.

* **Lobby Properties** define general settings of the game. In a strategy game, it might be the speed of the game, map size, bot difficulty and etc. These properties are **synchronized**, meaning that when they change, members of the lobby will see those changes.
* **Team Properties** define properties of each specific team. These are **not synchronized**, and should be set when constructing the lobby itself. For example, in a 5 vs 5 game, one of those properties could be a **color** of the team.
* **Member Properties** represent player settings. For example, in an FPS game these can be used to set player class, character, loadout or other settings.

All of these settings can be accessed by game server later on.

### Lobby State

Lobby contains a state property, which is a value of `LobbyState` enum. This state changes during the lifetime of the lobby like this:

1. New lobby is in the `Preparations` state. In this state, players are allowed to pick teams, change their settings and etc.
2. When players are prepared, lobby switches to `StartingGameServer` state, and awaits for the game server to be started
3. When the game server is ready, it switches to the `GameInProgress` state
4. When the game is over, it's in the `GameOver`state

### Flow of Events When Creating A Lobby

1. User makes a request to create a lobby. This request consists of **Lobby Factory id** and **properties** dictionary.
2. Master server handles the requests, and finds a specific factory
3. Lobby factory uses the settings provided to create the lobby.
4. Player is informed about successful creation of lobby
5. Player sends a request to get all of the lobby info
6. Player displays that information

## Quick Tutorial

### Create a New Lobby Factory

Create a new script and call it `MyLobbyFactory`

``` C#
using System.Collections.Generic;

namespace Barebones.MasterServer
{
    public class MyLobbyFactory : ILobbyFactory
    {
        private readonly LobbiesModule _module;

        public MyLobbyFactory(LobbiesModule module)
        {
            _module = module;
        }

        public GameLobby CreateLobby(Dictionary<string, string> properties)
        {
            // Create the teams
            var teamA = new LobbyTeam("Counter Terrorists")
            {
                MaxPlayers = 5,
                MinPlayers = 0
            };
            var teamB = new LobbyTeam("Terrorists")
            {
                MaxPlayers = 5,
                MinPlayers = 0
            };

            // Set their colors (this could be any property)
            teamA.SetProperty("color", "0000FF");
            teamB.SetProperty("color", "FF0000");

            // Create configuration
            var config = new LobbyConfig();
            config.EnableManualStart = true;
            config.AllowJoiningWhenGameIsLive = true;
            config.EnableGameMasters = true;

            // Get the lobby name
            var lobbyName = properties.ContainsKey(GameProperty.GameName)
                ? properties[GameProperty.GameName]
                : "Default name";

            // Create the lobby
            var lobby = new GameLobby(_module.GenerateLobbyId(),
                new[] { teamA, teamB }, _module, config)
            {
                Name = lobbyName,
                Type = Id
            };

            // Override properties with what user provided
            lobby.SetLobbyProperties(properties);

            // Add control for the game speed
            lobby.AddControl(new LobbyPropertyData()
            {
                Label = "Game Speed",
                Options = new List<string>() { "1x", "2x", "3x" },
                PropertyKey = "speed"
            }, "2x"); // Default option

            // Add control to enable/disable gravity
            lobby.AddControl(new LobbyPropertyData()
            {
                Label = "Gravity",
                Options = new List<string>() { "On", "Off" },
                PropertyKey = "gravity",
            });

            return lobby;
        }
    }
}
```

This script will be responsible for creating lobbies from the user request.

### Register Factory to Lobbies Module

Let's add our new factory to our lobbies module. There are multiple ways to do it. We can call `lobbiesModule.RegisterFactory()` method, by adding this line of code anywhere in our scripts:

``` C#
FindObjectOfType<LobbiesModule>().RegisterFactory(new MyLobbyFactory("myFactory"));
``` 

Or by extending the LobbiesModule and overriding `SetupFactories()` method.

**myFactory** is the unique identifier of our new factory, by which lobbies module decides which factory to choose when it receives a request from user

### Sending a Request To Create a Lobby

From a client, you can call a static method `LobbiesModule.CreateLobby`, like this:

``` C#
            // Create the dictionary of lobby settings
            var properties = new Dictionary<string, string>()
            {
                {"map", "Super map" },
                {GameProperty.SceneName, "GameRoom" }
            };

            // Send a request
            LobbiesModule.CreateLobby("myFactory", properties, (id, error) =>
            {
                if (!id.HasValue)
                {
                    Debug.LogError("Failed to create the lobby: " + error);
                }

                Debug.Log("Lobby created successfully!");
            });
```

