### Builds!

Debugging:

The QuickStart example that comes with MSF enables development builds by setting the BuildOptions.Development flag. This doesn't allow script debugging by default, however. Just like the build settings page in the editor, there are two flags. You can also enable script debugging by setting your build options to

`BuildOptions.Development | BuildOptions.AllowDebugging`

(Note: As mentioned below, Linux headless mode is incompatible with dev mode and thus script debugging, so for production builds you'll have to turn this off! ;)

Player settings:

You may wish your players to start with certain default settings. For example, a default resolution, or to not display the resolution dialog, or even to change the .NET profile. When building from script, these are accessed through PlayerSettings. Here are some example settings:

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
Tip for Linux VPS folks: 
If you want to enable headless mode, and you're doing your build from script, like the menu customizations, you'll need to set BuildOptions to BuildOptions.EnableHeadlessMode. You can't '|' it with dev mode because the two build options are mutually exclusive! (Note: previous rev of this doc implied using & (bit-wise AND) to set two modes at once, but the correct operator is |, the bit-wise OR. )


Also:
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
