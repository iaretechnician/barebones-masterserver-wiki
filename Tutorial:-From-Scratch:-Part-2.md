Continuing from [Tutorial: From Scratch: Part 1](https://github.com/alvyxaz/barebones-masterserver/wiki/Tutorial:-From-Scratch:-Part-1)

In Part 1, we set up the MasterServer, set up the client, started the Master Server, then connected the client. Though it is quite a feat of networking and programming, there's nothing to show off. We're going to fix that now.

---

## Step 1:

First, make sure you have the project from the last tutorial opened up. You should have 2 scenes, **scratchClient** and **scratchMasterServer**.

We'll start with adding a **Spawner** to the MasterServer, so open **scratchMasterServer**.

Right now, the only thing that is visible is the button (if you made one). Let's add some UI elements so we can keep track of what's going on when the server is running.

### Connection status

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

Finally, we need to do some tidying up in case the object is destroyed while the scene is running. If we don't do this, the Connection will search for the function defined here 

