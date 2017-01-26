This chapter will focus on explaining how game servers can be started by spawner, or manually, by the developer.

ℹ️ To keep this tutorial technology independent, we'll be using a "**Fake game server**" that we've created on the previous chapter of the tutorial

## Overview

In many cases, game servers are using the same build as the client. This is done mainly because game server logic usually intertwines with clients (Prefabs, RPC's, collision meshes and etc).

So, if it's the same build, how does Unity tell if we want to run a client or game server? It doesn't, so we need to do it manually - usually with command-line arguments.

There are two main ways to start a game sever:
* **Manually / Directly** - this would, usually mean starting the build / executable by hand
* **Via Spawner server** - by sending a request to an appropriate spawner server, which would take care of starting the game server. Usually, **Master** server server sends a request to spawner, however, master server can take requests from users and send a request to spawner seamlessly (as if the user requested it directly).

## Starting a Game Server Manually

⚠️ All of the work will be done on the Client build

We'll need to write a script that checks if we have a specific command-line argument, and start a game server if we do.

### 1. Add a new method to `FakeGameServer.cs`

``` C#
    public void StartGameServer(string publicIp, int port)
    {
        // We're not using publicIp and port, but I'm adding this just for a demonstration,
        // because if you're using a real server, you'll most likely need this data
        OnStartClick();
    }
```

This method should do the same as clicking a "Start game server" button on the ui

### 2. Create a new script and name it "FakeGameServerStarter"

``` C#
using Barebones.MasterServer;
using UnityEngine;

public class FakeGameServerStarter : MonoBehaviour {

	// Use this for initialization
	void Start () {

        // Alternative: if (BmArgs.ArgExists("-myCustomArg"))
        if (BmArgs.StartManual)
	    {
            // If cmd args contain an argument "-bmStartManual"

            // You can extract other arguments in a similar fashion
	        var ip = BmArgs.MachineIpAddress;
	        var port = BmArgs.GamePort;

            // Start the game server
            FindObjectOfType<FakeGameServer>().StartGameServer(ip, port);
        }
    }
}
```

### 3. Add this new script to the scene

* Create a new empty game object and name it "ServerStarter"
* Add a "`FakeGameServerStarter`" component


### 4. Test if it works

* Make sure that **master server is running**
* Make a build
* Run it from command line, for example, like this:
  `./Build.exe -bmStartManual`

Here are the indications that game server is running:

![](http://i.imgur.com/J2HIvuc.png)

You can also try launching a client in the editor and click a "Get Games" button - it should retrieve your newly created game server.

## Starting Game Server With Spawner

This section will go through steps of explaining how any kind of game server can be started by a spawner server.

### ⚠️ On Master Server Project

### 1. Add a Spawner Server component

* Create a new, empty game object as a child of Networking
* Name it "Spawner Server"
* Attach a "SpawnerServer" component to it

![](http://i.imgur.com/icAwITW.png)

### 2. Configure Spawner Server

* Make sure you have a correct executable path (path to game server build)
* Uncheck **Run In Batch Mode**
* Check **Auto Start**, so that spawner would start automatically in editor
* Check **Wait For Master**, so that spawner starts only after the master server is started

![](http://i.imgur.com/qZbcXwr.png)

### Test if it spawner server is running

Hit a play button in the editor, and HUD should display that Spawner Server is running

![](http://i.imgur.com/g4ziXpG.png)

### ⚠️ On Client Project

On client project, we will add an input field and a button. Here's the flow of events that should happend:

* Client clicks a "Create game" button
* Sends a "create game server" request to Master Server
* Master server notifies a spawner to spawn a game server
* Game server starts

