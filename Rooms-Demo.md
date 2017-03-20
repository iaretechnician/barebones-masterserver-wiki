### Launching The Demo

#### Making a Build

Make a build with all the scenes, located at `Barebones/Demos/RoomsDemo/Scenes/` (they will probably start with "Demo-...")

1. File > Build Settings
1. Drag in all the scenes
1. :warning: Make sure that **Demo-SelectScene** is the first scene
1. Make a build 

![](http://i.imgur.com/9MKnb6Z.png)

#### Running the Demo

1. Start **Master Server** and **Spawner**
   1. Open your build (run the "game")
   1. Click "Master + Spawner" button
   1. Click "Start Master Server"
   1. Click "Start Spawner"
1. Starting the **client**
   1. Open another instance of your build (run the "game")
   1. Click "Client Scene"
   1. Click "Play as Guest"
   1. Click "Create Game Server"
   1. Wait for the server to be spawned
   1. Play the game

### Running the server from command line

Basic setup: 

`./Build.exe -msfStartMaster -msfStartSpawner`

Start spawner only:

`./Build.exe -msfStartSpawner`

Using a different build for game server, and limiting the number of processes than can be started:

`./Build.exe -msfStartSpawner -msfExe 'D:/SomethingElse/build.exe'` -msfMaxProcesses 10
