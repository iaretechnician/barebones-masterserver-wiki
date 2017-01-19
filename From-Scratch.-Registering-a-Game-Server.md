This tutorial will focus on showing you how master server finds out about existing game servers, and how they appear on the "games list".

To make it easier to understand, we will first try to register a "fake" game server, because this way it's easier to see how the whole process works in general, and how you can **register any kind of game server**.

## How Game Servers Are Registered?

When game server starts, it should "register" to master server, so that master server knows about it's existence, and can inform players about it (via "games list" view or any other way).

In essence, to register a game server, you'd need to call `GamesModule.RegisterGame()`, and it will have the game appear in the "games list". In fact, you don't even need a real game server to register it to the list - it can be just a fake / mock / empty object, which implements `IGameServer` interface.

So `GamesModule.RegisterGame()`takes these parameters:

* `RegisterGameServerPacket` - information about the game server ⚠️ _This parameter will most likely be removed in 1.03.2, because the `IGameServer` already has a method for creating it._
* `IGameServer` implementation - I'm not sure if generic implementation will work for you (), but you can see how specific methods are implemented, and do something similar
* GameServerRegisterCallback - callback, which will be called when the game server is registered to master server. This callback receives RegisteredGame object, which is sort of a link between master server and game server. You'll need to call `registeredGame.Open()` to make the game visible in the Games List