### What it's all about?

⚠️ Available only from **V2.0.2**

Master Server Framework is extremely customizable, but that comes with a price - it might be a bit difficult to understand how everything ties together. To help you ease into the framework, I made this guide and added a "QuickSetup" demo.

This is **NOT** the only way to work with the framework, nor is it the best (there's really no best way to use it). However, I think it's the easiest to understand and get into, so let's do it!

## Test it first

⚠️ By default, this demonstration is set up to work on **Windows** only. For other platforms, you'll need to edit the `Barebones/Demos/QuickSetup/Editor/MsfQuickBuild` script. You'll also need to change the "Executable Path".

1. Go to `Tools > Msf > Build All` and select a folder, to which you want to save the binaries (builds with .exe files).
2. Wait for the building process to complete
3. Go to the specified folder and open `MasterAndSpawner.exe`
   1. Click "Start Master Server" button
   1. Wait until you see "Connected" at the top right corner
   1. Click "Register Spawner" button
4. Now open the `Client.exe`
   1. Hit "Play as guest"
   1. Click "Games List
   1. Click "Create Game Server"
   1. Click "Create"
   1. Game server should start, and you should automatically connect to it

## How it works

When you hit `Tools > Msf > Build All`, `MsfQuickBuild.cs` script creates two builds:

1. For Master and Spawner servers
1. For Client
1. For Game Server

#### 1. Master And Spawner Build

Scene: `QuickSetup/Scenes/MasterAndSpawner`

If you look in the hierarchy, these are the **Game Objects of interest**:

* MasterServer - this objects starts the master server
* ConnectionToMaster - this object tries to connect to master server
* Spawner - registers a spawner to master server (it first needs to connect to it, which is why we have `ConnectionToMaster` object)
* DemoPlayerProfiles - a simple module, which construct player profiles on master server

Normally, you should start master server and spawner by using command line arguments, but for this example, I decided that it might be a bit easier to understand if I added UI for manual controls. This way, you can start it both manually and by using command line arguments (you'll find more information below).

#### 2. Client build

Scene: `QuickSetup/Scenes/Client`

**Game Objects of interest**:

* ConnectionToMaster - this script automatically connects to master server
* UnetConnector - when client wants to enter a room, he receives a `RoomAccess` (which has game server ip, port and scene name). This script waits for access to be received, and then loads the appropriate scene.
* Canvas/MsfUiCombined - this object contains all of the ui components from ui examples that come with master server framework.

