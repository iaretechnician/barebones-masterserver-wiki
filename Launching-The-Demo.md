Add two scenes to the build settings (File > Build Settings) : `AwesomeGameMain` and `AwesomeGame`.
Make sure `AwesomeGameMain` is the first one

![](http://i.imgur.com/IK4RaI7.png)

Now make a standalone build somewhere, for example at `C:/Build/DesktopBuild.exe`.

Open the `AwesomeGameMain` scene, in the hierarchy, locate `Barebones >  SpawnerServer`, and change the **Override Exe Path** property in the inspector to your build path. _This path will be used to start a game server process when you're in the editor._

![](http://i.imgur.com/7qpE7RY.png)

Now, with `AwesomeGameMain` scene in the editor, you should be able to hit a **Play** button in the editor, and the example should work. A HUD at the bottom right should show an indication that Master and Spawner servers were started, and the client has connected to the master:

![](http://i.imgur.com/xw8xZ31.png)

At this point, you should be able to log into the game, and create a new room. You should also be able to start a client from the build you just made, and connect to the new room.