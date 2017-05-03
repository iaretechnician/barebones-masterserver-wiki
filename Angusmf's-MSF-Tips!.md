### Builds!

General:

 A very useful feature of MSF is the collection of menu items that you can click to automatically build players for the QuickStart demo scenes​. They are a great example to start from when automating builds for your own project. Something that is really cool in Unity is that you can call methods like these from the command line! This means you should be able to fire off new builds without even starting the editor​! To change the settings and output of your builds, you'll need to be able to tweak some settings. These tips cover some of the useful settings you may need.

When building from script, such as in the menu options provided for the QuickStart example in MSF, there are ways to achieve all the same

BuildOptions

BuildTargetGroup (Unity 5.6)

BuildTarget

PlayerSettings


You'll probably find you have default settings you want to apply to your all of your builds, whether they are client, server, master, spawner, windows, Linux...then edit specific settings per-build before each call to BuildPipeline.BuildPlayer.

To start with, set BuildOptions to

 `BuildOptions.None`

 for a default. You can set it to

` BuildOptions.Development | BuildOptions.AllowDebugging` 

before development builds to allow debugging via Mono or Visual Studio and to enable the dev console in the player. When building for headless Linux (aka, a VPS) you must _replace_ the value in BuildOptions with 

`BuildOptions.EnableHeadlessMode`

or you will not be able to run in batchmode, and the executable will crash.  AllowDebugging and Development cannot be set at the same time as EnableHeadlessMode! (On Windows, EnableHeadlessMode does not seem to be required, so dev/debug builds may run headless.)

BuildTargetGroup (Unity 5.6+) can be set to Standalone for Linux, Windows and Mac, but must be set to the correct target for WebGL, Android, etc.

BuildTarget should reflect the actual player type you are trying to build and is more granular than the BuildTargetGroup setting. For instance, you must specify 32 or 64 bit when building standalone. 64-bit windows is pretty forgiving and will run any Windows build, but Linux, in my testing, did not like the "Universal" build target, so I specifically built for `StandanloneLinux64`, which worked on local test VMs and my VPS.

PlayerSettings allows you to set the small details of each player you build, such as most of the player-specific settings you see in the editor. See below for some reasonable defaults that you can play with.

(note that _buildTargetGroup is a reference to a BuildTargetGroup that will be used when the player is built. Replace with your own reference.)

`PlayerSettings.SetApiCompatibilityLevel(_buildTargetGroup, ApiCompatibilityLevel.NET_2_0_Subset);`

`PlayerSettings.defaultIsFullScreen = false;`

`PlayerSettings.defaultScreenHeight = 768;`

`PlayerSettings.defaultScreenWidth = 1024;`

`PlayerSettings.runInBackground = true;`

`PlayerSettings.displayResolutionDialog = ResolutionDialogSetting.Disabled;`

`PlayerSettings.resizableWindow = true;`

or if you are using a version of Unity previous to 5.6, change the first line to:

`      PlayerSettings.apiCompatibilityLevel = ApiCompatibilityLevel.NET_2_0_Subset;`



### Linux
See above for Linux build settings.

To run your executables on headless Linux, you'll need to be aware of a few things (see [Tutorial: Building to Linux VPS](https://github.com/alvyxaz/barebones-masterserver/wiki/Tutorial:-Building-to-Linux-VPS) for much more detailed information if needed.)

Make sure you enable owner execution of the binaries (chmod 7xx)

On Debian, the libgl (I think) lib was missing, so I had to install with apt. I suspect most server images will be missing this unless there is a UI. 

_If your binary is built for the correct target_ (mine was StandanloneLinux64,)  then running

 ` ldd [nameofbinary] `

in that dir lists all the dependencies and shows you any that are missing. Googling the error was enough to find how to install it.

This page has some good applicable information (noobtuts sells several assets on the store that are game frameworks with this sort of hosting integrated. Worth a look.) -  [noobtuts - UNET Server Hosting Tutorial](https://noobtuts.com/unity/unet-server-hosting)


### Starting Game Server from Command Line or UI

This tip is for starting a game server "manually" rather than from a Spawner.

Refer to step 3 in the link below:

[https://github.com/alvyxaz/barebones-masterserver/wiki/Quick-Setup](https://github.com/alvyxaz/barebones-masterserver/wiki/Quick-Setup)

If you add all of these game objects to your scene except for:

* UnetServerStarter - you don't need it, because you'll start server in a different way
* RoomTerminator - If you're not going to destroy the game server after the last user leaves

You'll be left with:
* UnetGameRoom - which automatically registers a room
* ConnectionToMaster - which connects to master
* UnetConnector - which connects players to the game server

You could then add a script to your scene with a button which starts a game server

            void OnStartServerClick() {
                FindObjectOfType<NetworkManager>().StartServer();
            }

--Or--

You could add a script like


            if (Msf.Args.IsProvided("-startMySuperServer"))
            {
                FindObjectOfType<NetworkManager>().StartServer();
            }

    
And start your game server with command:

./Build.exe -startMySuperServer
