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

