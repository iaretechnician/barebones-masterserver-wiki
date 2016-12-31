### History:

* :warning: Current version: **v1.03**. Previous: [v1.02](https://github.com/alvyxaz/barebones-masterserver/wiki/Game-Servers/5e24bfd52ee7a50744fa3630973213c6baed8b6d)

### Warning

⚠️ 🔴 Make sure to build your game so that it can **run in the background** - it's necessary for any game that needs networking. Otherwise, you might experience some nasty glitches. You'll find the setting in **File > Build Settings > Player Settings > Run In Background**

## How Clients Connect To Game Server

Scheme below illustrates the process of joining a game server:

![](http://i.imgur.com/E6a8qCZ.png)

1. Client sends a request with `GameId` to Master Server. Requests are indirect for security purposes
2. Master server finds a game server with the given id and transfers the request to that server. Before transferring the request, master can check if the user is authorized, if the game password is correct and etc. 
3. After receiving the request, game server check if the user can actually join the game. These checks might be simple checks to see if the server is full, or you could implement a banned players list within game server.
4. If the player can join the game, game server creates an **Access Token** and sends it back to client. Actually, packet contains a little more info:
   * AccessToken
   * Public Address - so that client knows where to connect
   * SceneName - so that client knows which scene to switch to
5. Data is passed back to client 
6. Client connects to game server
7. Game server starts a **Timeout** period, after which, if no **access token** is received from the user, user connection is terminated
8. User sends an access token to the game server
9. Game server uses the **access token** to find out which player joined, and then spawns that player

:information_source: When token is generated, game server **reserves** a slot for player - this ensures that an accident, where player loads the scene just to see that the server is full, doesn't happen. After creating access token, game server waits a limited amount of time before invalidating the token.

## Registering Game Server to Master

If we ignore different server starting scenarios, there are five steps you need to take to prepare your game server to work with master

1. Make sure your game server implents the interface `IGameServer`. You'll find more info on what each method means and stands for in the comments of the interface file.
1. Start game server. This step will depend on game server technology you are using. In uNET HLAPI you'll probably want to call `networkManager.StartServer()` or use the HUD script provided by unity
1. Connect Game Server to Master. You can do that by calling
`Connections.GameToMaster.Connect("127.0.0.1", 5002)`
1. Register game server by calling:
`GamesModule.RegisterGame(registrationPacket, server, (game) => {})`
1. Open game server to public. You can do that right after registration, by invoking `game.Open()`

:information_source: If you are wondering why you can't make a simple call to master server and register without implementing IGameServer, you actually can. Under the hood, registration is quite simple. Inspect `GamesModule.RegisterGame` for more info.

## Gameplay Networking

Master Server Framework is mainly just that - a framework for kick-starting your back-end server. There are no scripts that do gameplay networking (for example, synchronizing animations and Transform's between clients, shooting and etc.).

The reason why gameplay networking was not included is simple - there are many popular alternatives you can use and might be familiar with.

In my games, I use **uNET**. It has some caveats, but it's free and maintained by Unity - you can be sure they are not dropping this any time soon. You should be able to use Forge Networking or any other solution **that allows you to Host** your games.

## Types Of Game Servers

### Dedicated Server
This is a type of server which is created by developers and most likely is always online. In general, here's how you start a dedicated server:

1. Developer starts the game executable with command line arguments (more info)
2. Game application starts (and starts the server)
3. Game server registers to master
4. Game server sets itself as open

### User Created Server

This is, as the name suggests, a server, created when user requests it. The starting process has more steps than dedicated server, but in the end, the process is pretty much the same: 

1. User sends a request to Master Server
1. Master Server finds an appropriate spawner, which is not busy (by region or something else)
1. Master Server sends a request to Spawner to start a new server
1. Spawner starts game executable with command line arguments
1. Game application (game server) starts and connects to Master Server
1. Game Server notifies Master Server that the executable is started (not the game server itself)
1. Master Server sends Game Server setup information (inputs by user and etc.)
1. Game Server starts the actual game server _(from this point, it's the same as dedicated)_
1. Game Server registers to Master
1. Game Server sets itself as open