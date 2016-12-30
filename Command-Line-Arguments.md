### History:

* :warning: Current version: **v1.03**. Previous: [v1.02](https://github.com/alvyxaz/barebones-masterserver/wiki/Command-Line-Arguments/9b1049e46a506702434153d220bfffdd85aab02a)

## Examples

Examples of the commands will be given in a context, where main executable file is named `DesktopBuild.exe`, and command line / terminal is opened at the same path the file is in.

Starting a master server with default parameters:

`./DesktopBuild.exe -bmMaster`

Starting a master server with a different port for clients

`./DesktopBuild.exe -bmClientsPort 85`

You can start both, master server and spawner server in one instance:

`./DesktopBuild.exe -bmMaster -bmSpawner`

Starting a game server manually (capped at 20 fps, on scene "GameRoom", port 7777, with name "New Name"):

`./DesktopBuild.exe -bmStartManual -fps 20 -bmScene GameRoom -bmGamePort 7777 -bmGameName "New Name"`

## List Of Supported Arguments

Arguments are parsed in the `BmArgs` helper class

| Argument       | Default value| Description|
| -------------  |:-------------:| -----:|
| **Master:** |  |  |
| -bmMaster | no set | If found, Master server will be started|
| -bmMasterKey | "" (empty string)| Key, which is used by master server and **game servers** (when registering to master, they must match)|
| -bmClientsPort | 90 | Port opened in master server, to which clients will connect |
| -bmGamesPort | 5001 | Port opened in master server, to which game servers will connect |
| -bmSpawnersPort | 5002 | Port opened in master server, to which spawner servers will connect |
| **Spawner:** |  |  |
| -bmSpawner| not set| If found, Spawner server will be started|
| -bmMinPort | 1000| When Spawner server starts a game server, it will assign to them ports starting from this number|
| -bmMaxPort | 2000| |
| -bmMaxGames| 5 | Max number of game servers this spawner can spawn|
| -bmExe | "" (empty string)| Path to executable, which should start a game server. If empty, same executable, on which spawner is running, will be used |
| -bmBatchmode | not set | If found, spawner server will start game servers in batchmode|
| **Game Server:** |  |  |
| -bmScene | null | If this argument is found, game server will first switch to that scene before starting a game server|
| -bmIp | 127.0.0.1 | Public ip of the machine. This is used by game servers, so they know their public IP and can send it to clients, to let them know where to connect |
| -bmStartSpawned :new: | not set | If this argument is found, it's considered that game server is being spawned by spawner |
| -bmStartManual :new: | not set | If this argument is found, it's considered that a game server is being started manually |
| -bmSpawnId :new: | -1 | Spawn id, which is used to tell which process was started before starting a spawned game server |
| -bmPrivate | not set | If arg is found when launching a game server, it will not appear in the listings|
| -bmMasterIp | 127.0.0.1 | Ip of the master server, to which game servers and spawner servers will try to connect|
| -bmGamePort | 7777 | Read by game server, to know at which port to open the socket |
| -bmGameName | "Dedicated Server" | Name, used by game server |
| -bmMaxPlayers| 10 | Max number of players the server can hold |
| -bmFps | 30 | When starting a server, this fps limit will be applied to not overload CPU |
| -bmPassword | "" (empty string) | Password of the game room|
| -bmWebsockets | not set | If this argument is found, game server will be started as websocket |