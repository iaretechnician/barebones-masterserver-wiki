(Quick write-up before I forget what I did)

We are using the QuickSetup demo for this tutorial. It has everything needed to get started with minimal work.  
As we need to make a few minor changes, you can either edit it directly which may get overwritten when the assets are updated, or you can copy the QuickSetup directory and edit it there.

The changes are very few and very minor, so it's not much lost if it's overwritten.

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

1. Select only 