![](http://i.imgur.com/IHbHOxT.png)

Navigate to folder `Barebones/Demos/MainDemo/Scenes` and add all of those scenes to your build.
Make sure `DemoMain` is the first one

![](http://i.imgur.com/kXHUGbY.png)

Open the `DemoMain` scene, in the hierarchy, locate `Barebones >  SpawnerServer`, and change the **Override Exe Path** property in the inspector to your build path (for example `D:/Build/Win/Build.exe`). _This path will be used to start a game server process when you're in the editor._

Now make a standalone build to the same path.

![](http://i.imgur.com/D6vJmMs.png)

Now, with `DemoMain` scene in the editor, you should be able to hit a **Play** button in the editor, and the example should work. A HUD at the bottom right should show an indication that Master and Spawner servers were started, and the client has connected to the master:

![](http://i.imgur.com/xw8xZ31.png)

At this point, you should be able to log into the game, and create a new room. You should also be able to start a client from the build you just made, and connect to the new room.

## Starting a World Demo

If you've tried to open the world demo, you might have received an error indicating that a specific zone cannot be found. This is because you need to start zone servers. There are two ways to do it.

If you're starting a master server from the editor, there's a flag on the **WorldGame** object, called `DontSpawnZonesInEditor`. If you uncheck it, world demo will start game zones together with the master server, on editor.

![](http://i.imgur.com/VFpneJ0.png)

Another approach could be to start the master server from the build, and add a `-spawnZones` flag to the command line arguments. You would start your server with a command similar to this:

`./Build.exe -bmMaster -bmSpawner -spawnZones`

:warning: If you run master server from a build and connect to it with a client in the editor, you might encounter a problem, because client in the editor will try to start another master server. To avoid this, you can unset a flag **AutoStartInEditor** on the **Master** object (in hierarchy `Barebones > MasterServer`)
