Most of the time, when you want to extend the functionality of MSF, you'll be writing custom modules to do that.

### What are the modules?

In essence, a module is just a game object, which is initialized by the Master Server. When client interacts with master server, he sends messages. So modules are all about handling those messages. Here's how modules are picked up and initialized:

1. Master Server begins "starting"
1. Master Server looks for all of the modules
1. All of the modules are "registered" to master server
1. Master starts initializing modules.
1. If the module has dependencies, it won't be initialized, until it's dependency is initialized
1. Master server is started

### Creating a basic module

To create a module, you'll need to create a new script which extends `ServerModuleBehaviour`. You should also override the `Initialize` method. Here's an example of the most basic module you can have:

``` C#
using Barebones.MasterServer;
using UnityEngine;

public class CustomModule : ServerModuleBehaviour {

    public override void Initialize(IServer server)
    {
        base.Initialize(server);
        Debug.Log("Custom Module initialized");
    }

}
```

### Dependencies

Modules can register dependencies on other modules, which must be done before the master server starts. Dependencies can be **required** or **optional**. 

It's recommended to set dependencies on `Awake` method. This will make sure that dependencies are registered before master server actually starts.

Here's an example, which demonstrates dependencies:

``` C#
using Barebones.MasterServer;

public class CustomModule : ServerModuleBehaviour {


    void Awake()
    {
        // Add dependency to auth module
        AddDependency<AuthModule>();

        // Add optional dependency to profiles
        AddOptionalDependency<ProfilesModule>();
    }

    public override void Initialize(IServer server)
    {

        // If initialize method is called, we are sure that our dependencies 
        // were already initialized
        var auth = server.GetModule<AuthModule>();

        var profiles = server.GetModule<ProfilesModule>();

        // Since this is an optional dependency, it might not exist on the master server
        if (profiles != null)
        {
            Logs.Warn("Profiles module is not on the scene");
        }

    }

}

```

### Handling messages

Here's an example of a custom module, which handles a message from client, and responds with a number of connected users:

``` C#
using Barebones.MasterServer;
using Barebones.Networking;

// Custom op codes
public enum CustomOpCodes
{
    GetOnlineCount = 0
    // (OpCodes should be unique. MSF internal opCodes 
    // start from 32000, so you can use anything from 0 to 32000
}


/// <summary>
/// Our custom module
/// </summary>
public class CustomModule : ServerModuleBehaviour
{
    private AuthModule _auth;

    void Awake()
    {
        // Add dependency to auth module
        AddDependency<AuthModule>();
    }

    public override void Initialize(IServer server)
    {
        _auth = server.GetModule<AuthModule>();

        server.SetHandler((short) CustomOpCodes.GetOnlineCount, HandleGetOnlineCount);
    }

    private void HandleGetOnlineCount(IIncommingMessage message)
    {
        // Respond with a number of online users
        message.Respond(_auth.LoggedInUsers.Count);
    }
}

```

On the client, you can send the message like this:

``` C#
Msf.Connection.SendMessage((short) CustomOpCodes.GetOnlineCount, (status, response) =>
{
    Debug.Log("Logged in users count: " + response.AsInt());
});
```