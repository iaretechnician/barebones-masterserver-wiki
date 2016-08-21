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

## Registering Game Server to Master

If we ignore different server starting scenarios, there are five steps you need to take to prepare your game server to work with master

1. Make sure your game server implents the interface `IGameServer`. You'll find more info on what each method means and stands for in the comments of the interface file.
1. Start game server. This step will depend on game server technology you are using. In uNET HLAPI you'll probably want to call `networkManager.StartServer()` or use the HUD script provided by unity
1. Connect Game Server to Master. You can do that by calling
`BConnection.Connect(BConnectionId.GameServerToMaster, "127.0.0.1:5002", (connection) => {})`
1. Register game server by calling:
`BMMaster.RegisterGame(registrationPacket, server, (game) => {})`
Open game server to public. You can do that right after registration, by invoking `game.Open()`
