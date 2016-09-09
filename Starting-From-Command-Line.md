## List Of Arguments

| Argument       | Default value| Description|
| -------------  |:-------------:| -----:|
| -bmStartUserCreated | not set | If this argument is found, it's considered that user created server needs to be started. |
| -bmStartDedicated | not set | If this argument is found, dedicated game server will be started |
| -bmInstanceId | -1 | Instance id, which is used to tell which process was started before creating a user created game server|
| -bmPrivate | not set | If arg is found when launching a game server, it will not appear in the listings|
| -bmMasterIp | 127.0.0.1 | Ip of the master server, to which game servers and spawner servers will try to connect|
| -bmIp | 127.0.0.1 | Public ip of the machine. This is used by game servers, so they know their public IP and can send it to clients, to let them know where to connect |
| -bmGamePort | 7777 | Read by game server, to know at which port to open the socket |
| -bmGameName | "Dedicated Server" | Name, used by game server |
| -bmMaxPlayers| 10 | Max number of players the server can hold |
| -bmFps | 30 | When starting a server, this fps limit will be applied to not overload CPU |
| -bmMasterKey | "" (empty string)| Key, which is used by master server and game servers (when registering to master, they must match)|
| -bmPassword | "" (empty string) | Password of the game room|
| -bmWebsockets | not set | If this argument is found, game server will be started as websocket |
| **Master:** |  |  |
| -bmMaster | no set | If found, Master server will be started|
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