## Essence of the framework

No matter what you're building with the framework, there will always be two main components:

* **Master Server** - a central server, to which everything connects - players, game servers, spawners and etc.
* **Connection To Master Server** (Client) - connection, over which clients communicate with the master server

![](http://i.imgur.com/80FNqw3.png)

## The Most Basic Setup

1. Create new scene
1. Drag a `Barebones/Msf/Prefabs/MasterServer` prefab to scene
1. Drag a `Barebones/Msf/Prefabs/ConnectionToMaster` prefab to scene

And that's it!

> This is the core concept of the framework, and it's **all you need to work with it**. Everything else is just examples of how to use the API, and some helpfui UI components if you're too lazy to make your own.

If you hit the "Play" button in editor, you should see two messages:

![](http://i.imgur.com/DkdPOy8.png)

⚠️ Master server starts automatically only in **editor** (if AutoStartInEditor is checked). If you want to start it from the build, you'll have to make a custom button to do that, or start the build with "-msfStartMaster" argument.

ℹ️ `Barebones/Demos/Basics/Scenes/Basics-1. Master And Client` scene contains an example, where master server and client connects manually, when user clicks a button.

## How does this work?

* **MasterServer** prefab starts the master server
  * Initializes all of the modules that it can find in the scene.
* **ConnectionToMaster** keeps trying to connect to the master server automatically, so when master server is started, connection is established.

So with these two prefabs, your scene becomes both - the master server, and the client.

## Can I have the server and client in different scenes?

Of course, just add the **MasterServer** to one scene, and **ConnectionToMaster** to another. In fact, you can have them in completely **different projects**! 

There's really no "best" setup when working with the framework - you do what suits you best.

## Starting server and client from build

It's probably not enough to see the console message, especially if we're making a build, so let's add a simple HUD indicator, which would show us the status of connection, and whether or not the master server has started

1. Create Canvas in the scene, by going to `GameObject > UI > Canvas`.
2. Drag a prefab `Barebones/MsfUiSamples/Prefabs/MsfHUD` as a **child of Canvas**

Hit a Play button, and you should see the HUD ( ℹ️ you might need to click on the header to expand it)

![](http://i.imgur.com/abslSZA.png)

## What's next?

* Start working with the API
* Write your own modules
* Learn to use the Spawner (if you need it)