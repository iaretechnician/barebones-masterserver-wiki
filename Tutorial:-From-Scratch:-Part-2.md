Continuing from [Tutorial: From Scratch: Part 1](https://github.com/alvyxaz/barebones-masterserver/wiki/Tutorial:-From-Scratch:-Part-1)

In Part 1, we set up the MasterServer, set up the client, started the Master Server, then connected the client. Though it is quite a feat of networking and programming, there's nothing to show off. We're going to fix that now.

---

First, make sure you have the project from the last tutorial opened up. You should have 2 scenes, **scratchClient** and **scratchMasterServer**.

We'll be adding a **Spawner** to the MasterServer but we need to know if the scene is connected first.

## Step 1:

Open **scratchMasterServer**.

Right now, the only thing that is visible is the button (if you made one). Let's add some UI elements so we can keep track of what's going on when the server is running.

At the root of the scene, you have an object named "ConnectionToMaster". We are going to make a textbox that displays the status of the connection the script on that object created.

Add a **Text** UI element to the canvas. Name it "ConnectionStatus". Add a new script to the object called "DisplayConnectionStatus".

In the script, you will need to include 
```
using Barebones.MasterServer;
using Barebones.Networking;
using UnityEngine;
using UnityEngine.UI;
```

to cover everything we will be using.

The variables we need to define are `private IClientSocket _connection;`, to hold the connection that was made in "ConnectToMaster", and `public Text Text;`, to hold a reference to the Text field of the object.

In the **Start** function, we need to define these variables.

```
Text = gameObject.GetComponent<Text>();
_connection = GetConnection();
```

You should be familiar with "GetComponent" and the "GetConnection" function we are going to define after adding the following after the above lines:

```
_connection.StatusChanged += UpdateStatusView;
UpdateStatusView(_connection.Status);
```

The "StatusChanged" event is invoked when, oddly enough, the status of the connection is changed. As this is evoked, the function we added, "UpdateStatusView", is called using the new status of the connection.
The second line is simply calling the function manually to display the proper message when the scene is started.

Now for the "GetConnection" function

```
protected IClientSocket GetConnection()
{
    return Msf.Connection;
}
```

Nothing fancy, just something to return the connection that was established in the "ConnectToMaster" script.

Next, the main portion of the script, the switch that alters the displayed text.

```
protected void UpdateStatusView(ConnectionStatus status)
{
    switch (status)
    {
        case ConnectionStatus.Connected:
            Text.text = "Connected";
            break;
        case ConnectionStatus.Disconnected:
            Text.text = "Offline";
            break;
        case ConnectionStatus.Connecting:
            Text.text = "Connecting";
            break;
        default:
            Text.text = "Unknown";
            break;
    }
}
```

The switch is based on the ConnectionStatus that is sent when the StatusChanged event is evoked. Depending on the current status, the switch will change the text to display properly.

Finally, we need to do some tidying up in case the object is destroyed while the scene is running. If we don't do this, the Connection will search for the "UpdateStatusView" function defined here and return an error when it can't find the object the script is attached to.

```
protected void OnDestroy()
{
    _connection.StatusChanged -= UpdateStatusView;
}
```

And that's it for this script. Next, we'll add the **Spawner**. We needed the status first though, as the Spawner can't be started until the scene is connected to the MasterServer.

---

## Step 2:

Before adding the spawner, we need to let the MasterServer know to expect the Spawner to connect.

On the MasterServer gameobject, add an empty component and name it "SpawnersModule" and add the component "SpawnersModule" to the new object.  
This gives the MasterServer the information that it needs to deal with requests to connect a new spawner, as well as what to do if a client requests for a spawner to do something (such as spawn a game server which we'll get to in the next tutorial).

Now for the actual spawner.

On the root of the scene, add an empty GameObject and name it **Spawner**. Add the "SpawnerBehaviour" script to the object as a component.

That's pretty much all there is to it. Once the MasterServer starts, the ConnectionToMaster script will set up the connection, and the Spawner will start up when it detects the established connection.

We're going to do a little extra though. Find the "SpawnerBehaviour" you just added and disable "Auto Start in Editor". This will prevent it from connecting immediately when the connection is established.

Next, duplicate the Start Master Server button and change the text to "Start Spawner". Change the OnClick event of the new button to **SpawnerBehaviour.StartSpawner**

Add a new text object as well and name it "SpawnerStatus". Add a new script called "DisplaySpawnerStatus".

This script is very similar and far simpler than the earlier one, so I won't be explaining as much.

First, the dependencies: 

```
using Barebones.MasterServer;
using UnityEngine;
using UnityEngine.UI;
```

Next, the variable. We don't need to reference the connection directly, it's covered in the **Msf** calls.

```
public Text Text;
```

Of course, we need to define the variable and add a function call to the SpawnerRegistered event in Start():

```
Text = gameObject.GetComponent<Text>();
Msf.Server.Spawners.SpawnerRegistered += OnSpawnerRegistered;
```

Now we have a new function. As there is no "status" to check, just the event of a Spawner being registered, we only need the one line:

```
private void OnSpawnerRegistered(SpawnerController obj)
{
    Text.text = "Started Spawner";
}
```

Finally, to avoid errors if the object is destroyed:

```
void OnDestroy()
{
    Msf.Server.Spawners.SpawnerRegistered -= OnSpawnerRegistered;
}
```

And that's all there is to that!

Start up the scene, press the "Start Master Server" button, and watch the connection status change.  
Next, do the same with the "Start Spawner" button.

The client hasn't been changed, but you can connect that too if you'd like.

---


Ok, so I lied. We didn't really make anything worth showing off, just something that lets us know if something went wrong. Anyway, this tutorial is running long, so I'm going to break it off here.

The next tutorial will cover adding GameServers to the project and connecting everything together.