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

In the **scratchMasterServer** scene, add a GameObject with the name "MasterServer" and give it the component `Master Server Behaviour`.  
Add another game object with the name "ConnectionToMaster" and give it the `Connection To Master` component. Check the "Connect On Start" box on the "Connection To Master" component.  
Make sure neither GameObject has any parents.

At this point, hit "Play" and check the console. If everything went well, you should see:

![](https://camo.githubusercontent.com/fc8f2b25064bdf222c7505b124adf164ec3d2a7a/687474703a2f2f692e696d6775722e636f6d2f446b64504f79382e706e67)


These steps are similar to those found in [Getting Started](https://github.com/alvyxaz/barebones-masterserver/wiki/Getting-Started). We're not using the prefabs to get a better feel for how everything works.

If you want to make your own prefabs as we go, you can use those in future projects and you will know much more about them than the ones in the `Barebones` folder. Or you can use the pre-made prefabs, I can't stop you, I'm just text on a screen.

---

## Step 3 - Setting up the Client

Open the **scratchClient** scene. Whether you use the pre-made prefab, the one you may have made in the last step, or make a new one from scratch, make a GameObject called "ConnectionToMaster" and give it the `Connection To Master` component. Check the "Connect On Start" box on the "Connection To Master" component.  

You might be wondering why we added the connection script to both scenes. The Client needs it to connect to the MasterServer, but why does the MasterServer need it? ... I actually don't know this one. (help!)

Anyway, back to where we were. 

If you were to start up the Client now, nothing would happen. At least not visibly. What is going on in the background is a loop of the client trying to connect to the MasterServer at the IP address 127.0.0.1 port 5000. Since there is no server running, it will just keep looping.

In order to have it do something, we need to have the server running before we start the client.

Which brings us to building the client and server!... After we add some extra bits to make the server work outside of the editor. If we built it as it is now, nothing would happen! An explanation follows-

---

## Step 4 - Prep for the build

Go back to the **scratchMasterServer** scene. Select the "MasterServer" gameObject. If you look in the Inspector, you'll notice there is a checked box "Auto Start In Editor". That's why the server starts up automatically when you hit play. However, this won't work once the game is built. Unmark the box and hit play. The console should remain empty, which is good, because nothing is happening!

In order to start the server from the build, we either need to use command line calls ( Explained in the last section of [Quick Setup](https://github.com/alvyxaz/barebones-masterserver/wiki/Quick-Setup) ), or have a button to manually call the `StartServer` function within `MasterServerBehaviour`.

We're going the button route here, if you want to do the command line, you can skip to Step 5.

First, create a canvas element, add a button to the canvas, and change the text in the canvas to "Start Master Server".

Select the "Button" object. In the inspector, look for "On Click ()". Add a command that targets MasterServer(MasterServerBehaviour). The function to call is `MasterServerBehaviour.StartServer`. Once that's selected, hit play and check the console.

At first nothing because we left the auto-start disabled. Now click the button. You should have the same entries in the Console that you had before. 

Now that that's all cleared up, onto the actual build!

---

## Step 5 - Building the Client and Server

If you go to `File->Build Settings`, you'll see a list of Platforms on the left, a few settings on the right, and an empty box titled "Scenes in Build".

Select the platform that you want to build for on the left, make sure you select the proper "Target Platform" and "Architecture" on the right, and clear the checkboxes.

Now, under the window, go to the `Assets->Scenes` folder we made in the first step. Drag the `scratchMasterServer` scene into the "Scenes In Build". That should be the _only_ scene in the build.

**IMPORTANT** Click on "Player Settings". The inspector should now display a somewhat intimidating list of checkboxes and dropdowns. Click on "Resolution and Presentation". Make sure the the "Run In Background" setting is **enabled**. If this isn't enabled, it can cause some issues or just not work at all.

Now that we've got that out of the way, click on "Build" in the "Build Settings" window.

After it finishes building, find the .exe and run it (windowed is probably a good option). Once it loads and you see your button, click on it. Nothing visibly happens, but in the background, the server has started up.  
*This may trigger your firewall, there is nothing harmful in the program you just made, so let it on through

Leave that running and head back to the editor and make sure you have **scratchClient** opened.

Click play, and watch the Console.

If everything went well, you should see the connection information in the console!

Congratulations, you have successfully built a server and connected to it through your client!

In the next part, we'll be adding some additional functionality and some GUI elements to keep track of everything. 