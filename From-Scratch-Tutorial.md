# Simple, minimum featured tutorial to introduce features of MSF
*This tutorial assumes basic knowledge of Unity and C#  
*Read [Getting Started](https://github.com/alvyxaz/barebones-masterserver/wiki/Getting-Started) first. It covers some concepts used in this tutorial

---

## Step 1 - Making the scenes

To start, make a new project named whatever you like. Make sure the entirety of the Barebones Master Framework asset is imported into your Assets folder.  
Though it will add an extra step or two, we are going to make 2 scenes, one for the Master Server and one for the Client. This gives the advantage over having everything in one scene, as it allows multiple clients to be run simultaneously without having to check which one is also the server.

In the Assets folder, make a new folder called `Scenes`. Save the current scene in the `Scenes` folder and name it **scratchClient**. Go to `File->Save Scene As` and save the scene again with the name **scratchMasterServer**.

Make sure you are editing the proper scene while following the instructions. There will be a title above every edit indicating the scene that should be open.

(Add image)

---

## Step 2 - Setting up the Master Server

In the **scratchMasterServer** scene, add `Barebones/Msf/Prefabs/MasterServer`. Make sure it doesn't have any parents.  
Do the same with `Barebones/Msf/Prefabs/ConnectionToMaster`.

At this point, hit "Play" and check the console. If everything went well, you should see:

![](https://camo.githubusercontent.com/fc8f2b25064bdf222c7505b124adf164ec3d2a7a/687474703a2f2f692e696d6775722e636f6d2f446b64504f79382e706e67)


(These steps were covered in [Getting Started](https://github.com/alvyxaz/barebones-masterserver/wiki/Getting-Started), so they should seem familiar)

---

## Step 3 - Setting up the Client