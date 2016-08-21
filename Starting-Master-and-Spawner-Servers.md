When setting up the framework, the first thing you should do is start Master Server, as it's the center point to which everything connects.

You can start the server by either starting an already compiled executable, or build and run the server from IDE.

## Running the server from IDE

1. Open the `MasterServer.sln` file with your IDE
1. Set `Barebones.MasterServer.Demo` as startup project
1. Right click on demo project -> Properties -> Debug, and add the command line arguments: `-master` . This will ensure that when you run the application, an argument to start master server is added.
1. Run the project through Debug -> Start Debugging (or Start Without Debugging)

You should start seeing some information on the screen. Probably something like this: 

![](http://i.imgur.com/iHpKJl2.png)

:white_check_mark: If there are no errors, master server should be running. 

## Running from pre-compiled exe

1. Start Command Prompt in **Complete **folder ([Windows tips](http://superuser.com/questions/946860/how-to-open-windows-cmd-so-that-it-starts-in-the-current-folder))
2. Run this command: 
    `Barebones.MasterServer.Demo.exe -master`

    This command starts master server with default parameters. More arguments are supported, and they can be found in section below. 

:white_check_mark: At this point, if there are no errors, the master server should be running

## Arguments

| Argument | Default value | Description |
|:------------- |:-------------|:-----|
| -master |  | If this argument is found, master server will be started | 
| -masterKey | "" (empty string) | Master key, which is used to check if connecting game servers have permission (know the key) | 
| -masterClientsPort | 80 | Port in master server, to which clients will connect | 
| -masterSpawnersPort | 5001 | Port int master server, to which spawner servers will connect | 
| -masterGamesPort | 5002 | Port in master server, to which game servers will connect | 
| -ip | 127.0.0.1 | <p>Public ip address of machine the server runs in.</p> <p>If starting Master Server, master server will bind to this address. Clients, spawners and game servers will use this address to connect to master server.</p> Spawner servers send this address to game servers when they start, so they can bind to this address. When clients try to connect to game server, they will use this address.| 
| -spawner |  | If this argument is found, spawner server will be starter | 
| -fpsLimit | 30 | This fps limit will be passed as cmd arg in started game | 
| -batchmode |  | If this argument is found, spawner will start game servers in batchmode | 
| -masterHost | (copies "-ip" value) | Public address of master server. Spawner server will use it to connect to master server | 
| -exe | ./ClientBuild/Build.x86 | Path to a file, which will be used to start a game server | 
| -portsRangeMin | 1000 | When spawner starts a game server, it will assing game server a port from range within -portsRangeMin and -portsRangeMax | 
| -portsRangeMax | 4000 | When spawner starts a game server, it will assing game server a port from range within -portsRangeMin and -portsRangeMax | 

## Argument Examples

Start both Master and Spawner servers:
     `-spawner -master`

Start just a Spawner server with default values, except for the path:
    `-spawner -exe D:/Build/Game.exe`


