We are using the QuickSetup demo for this tutorial. It has everything needed to get started with minimal work.  
As we need to make a few minor changes, you can either edit it directly which may get overwritten when the assets are updated, or you can copy the QuickSetup directory and edit it there.

The changes are very few and very minor, so it's no loss if it's overwritten.

Talk to Trivn in the [Discord](https://discord.gg/ZqGummZ) if you have any issues!

---

## Step 1:

Find your **VPS IP address**. You should be able to find it in the client area of the site you registered at.

You need to put this number in several places in the project. 
1. Open the **Client** scene.  
    * Find the "ConnectionToMaster" GameObject. In the inspector, find "Server IP" and put the **VPS IP address** in there.


2. Open the **GameServer** scene.  
    * Do the same as above.


3. Open the **MasterAndSpawner** scene.  
    * Do the same as above.  
    * Additionally, find the "Spawner" in the scene and change it's "Default Machine Ip" in the inspector to your **VPS IP address**.  
    * Finally, change the "Default Exe Path" to "GameServer.x86_64". (or .x86 if your VPS has a different architecture)
        * **NOTE**: Do not put "./" before the executable name! It seems strange, but for some reason, when running on linux, it assumes the entire entry is the path. If you are in the folder "/root/" it will end up looking for "/root/./GameServer.x86_64"

This ends the changes you need to make to the scenes themselves.

---

## Step 2:

Go to `File->Build Settings...". Add the four scenes: **Client**, **GameServer**, **MasterAndSpawner**, and **SimplePlatform** (located in the GameLevels folder).

Change the "Target Platform" to Linux. Change the architecture to either **x86** or **x86_64** depending on the architecture of your VPS.

Now to build the scenes:

### The GameServer

1. Select only the **GameServer** and **SimplePlatform** scenes.

2. Make sure "Headless Mode" is _ENABLED_.

3. Press build. It will be called **GameServer.x86_64** from here on.

### The Master Server and Spawner

1. Select only the **MasterAndSpawner** scene.

2. Make sure "Headless Mode" is _ENABLED_.

3. Press build. It will be called **MasterAndSpawner.x86_64** from here on.

### The Client

This step is optional. You can either build it for your local OS or just run it in the editor. Instructions follow for completion's sake.

1. Select only the **Client** and **SimplePlatform** scenes.

2. Make sure "Headless Mode" is _DISABLED_. (The option might not show up if you're not building for linux)

3. Press build. It will be called **Client.exe** from here on. (I'm on windows)

---

## Step 3:

I'm no expert in SSH or VPS, so I can't explain much here. I used WinSCP to move the files to my VPS. You can find tutorials with a quick search.

However you connect and upload the files, make sure you grab both **GameServer.x86_64** and **MasterAndSpawner.x86_64**, as well as their corresponding data folders. They should be placed in the same directory.

You'll now need to connect to your VPS with SSH. Again, not an expert. I use PuTTY to connect and open the command line. Make sure you know your username and password for the account (usually "root" and "" (nothing) for the default User/Pass).

From here, navigate to the directory that you uploaded the files to.

In order to run them, you need to make the files executable with `chmod 700 ./MasterAndSpawner.x86_64` and `chmod 700 GameServer.x86_64`

These shouldn't return anything. If they do and you can't figure it out on your own, feel free to contact us in the Discord for help!

After you have them both setup, you can run them. I recommend using two different command line windows so you can monitor the Master Server as well as the Spawner.

### Master Server

The parameter to start the Master Server is `-msfStartMaster`. You'll also need to tell it what IP address it is on with `-msfMasterIp xxx.xxx.xxx.xxx`. Finally, use `-logfile` to have the logs output to the command line window.

Here it is put together:

`./MasterAndSpawner.x86_64 -msfStartMaster -msfMasterIp xxx.xxx.xxx.xxx -logfile`

Replace xxx.xxx.xxx.xxx with your **VPS IP address**.

## Spawner

In a second window, you will use the parameter `-msfStartSpawner` to start the spawner. You can also `-logfile` to have to logs output to the window.

Put together:

`./MasterAndSpawner.x86_64 -msfStartSpawner -logfile`

---

And that's that! If there aren't any errors, your Master Server and Spawners should be up and running. To test it, either go to the editor and start the **Client** scene, or go to the Client build that you made and run it.

From here, you can either register or play as a guest. Once you're logged in, click on "Games List", then "CREATE GAME SERVER". The settings aren't important, click on "CREATE" and after a moment, the game should start.

If you see something about the process being aborted, it is probably an error on the naming of your GameServer or the Executable path. Check the Spawner and MasterServer windows and see what errors were thrown. Ask in the Discord channel if you need help!

When you're ready to shut down the Spawner and Master Server, use Ctrl+C in both command line windows to end the processes.

That's all there is to it, with this, you can upload your own game to your VPS and anyone with the client can play!

[Angusmf's MSF Tips](https://github.com/alvyxaz/barebones-masterserver/wiki/Angusmf's-MSF-Tips!) have some more information on building to linux if you want a more in-depth understanding.
