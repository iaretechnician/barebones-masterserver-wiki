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



