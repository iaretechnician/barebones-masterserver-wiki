![](http://i.imgur.com/IHbHOxT.png)

## Core Parts Of The Demo

At the moment of writing, there are two core parts in the demo:

* **World Demo** - main purpose of this demonstration is to show you how you can setup multiple game servers, and how players can go from one server to another. This demo can be helpful to those who want to use framework to construct large worlds, made of smaller chunks (zones). This demo was designed to be very simple, so other modules (like chat and player profiles) were not included.
* **Rooms Demo** - it demonstrates player's ability to create rooms (game servers), which are destroyed when last user leaves the game. It also demonstrates usage of **Profiles** and **Chat** modules. This demo will most likely be helpful to those who want to make rooms/sessions based game.

## Servers Architecture

Every server in the demo is running from a single Unity build.

![](http://i.imgur.com/SpDvYLo.png)

## World Demo Explained

This section contains a generalized flow of the events that happen during the demo

#### Initialization of the server

1. Master Server Starts
1. Master Server initializes our module (by calling `WorldDemoModule.Initialize()`)
1. Module looks if there's a Spawner server available.
1. If it is available, it requests Spawner to spawn two game servers (zone servers)
1. When our zone servers are started, they register to master server.

#### Player Jumping From One Server to Another

1. Player walks on a portal
1. **Game server A** "sees" that the player collided to the Portal
1. **Game server A** uses the portal name, and sends a teleport request to Master Server.
1. **Master server** analyzes the request and finds a game server with specific name (Game Server B)
1. **Master server** requests **Game Server B** to give player an access.
1. If Server B grants an access, **Master Server** stores that access in players **Peer Properties**, and responds to (Game Server A) about successful request.
1. **Game Server A** disconnects the player
1. After being disconnected, **Player** switches to **Loading Scene**
1. In the Loading Scene, player sends a request to **Master Server** to ask for a stored access to game server
1. When **Player** receives the access, he now knows where the other game server is (address and scene).
1. **Player** uses that access to get into different game server

ℹ️ This section describes how players go from one server to another in the demo. It doesn't mean that it's the only appropriate way to do it. Framework doesn't force any limits on this matter.