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