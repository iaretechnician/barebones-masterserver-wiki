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


