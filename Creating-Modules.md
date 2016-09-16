:warning: :warning: :warning:  At the time of writing, asset has been modified but the changes are not yet approved.  
:warning: :warning: :warning:  If you're using the initial version of the asset, you will need to use `Master` instead of   
:warning: :warning: :warning:  `IMaster` and opCodes and property codes should **start from 100 (101, 102, etc...)**  
:warning: :warning: :warning:  **You'll be able to use numbers from 0 after the upgrade**.

This section will guide you through the process of creating a custom master server module.

Creating modules is a preferred way of extending Master Server functionality. Modular approach can help you reuse components in other games, and it makes testing and debugging easier.

In this tutorial you will learn to:
* Send messages from client to server
* Handle messages (packets) from client
* Store data in the clients session
* Add new module to Master Server

## Defining Operation and Property Codes

When clients and servers exchange messages, they need to specify what kind of a message (message type) they send - this is done by setting the **OpCode** (operation code) when constructing the message.

You could specify random numbers from your head, but it's recommended to have all your opCodes in one location. For this example, we will store them in the class:

``` C#
    public class MyOpCodes
    {
        public const int GetPersonalInfo = 0;
        public const int SavePersonalInfo = 1;
    }
```

:warning: Some of the opCodes are occupied by the framework, but they start from 32000, so it's unlikely you will ever collide with them

We will also need to define property codes, which will be used when setting properties of `IPeer`.

``` C#
    public class MyPropCodes
    {
        public const int PersonalInfo = 0;
    }
```

## Creating a Custom Packet

When sending information, it's not always enough to send a simple string. When you need to send more complex data, you can make a class which extends `SerializablePacket`. You'll have to manually write the data to binary writer and read it with binary reader.

``` C#
    public class PersonalInfoPacket : SerializablePacket
    {
        public string Name;
        public int Age;

        public override void ToBinaryWriter(EndianBinaryWriter writer)
        {
            writer.Write(Name);
            writer.Write(Age);
        }

        public override void FromBinaryReader(EndianBinaryReader reader)
        {
            Name = reader.ReadString();
            Age = reader.ReadInt32();
        }
    }
```

:information_source: Serializing data manually is not the only way to do this. Depending on the platforms you are going to support, you can use other libraries, such as protobuf-net, or json.net. To avoid AOT and reflection code, framework does not use any of them, and relies on manual serialization for maximum compatibility.

## Sending the message

Code below demonstrates how you can send two types of messages:
* Reliable message without response
* Reliable message with expected response

Since these methods are static, you can add them anywhere you want, and invoke on client

``` C#
    public static void SavePersonalInfo(int age, string name)
    {
        // Construct the packet
        var info = new PersonalInfoPacket()
        {
            Age = age,
            Name = name
        };

        // Create a message
        var msg = MessageHelper.Create(MyOpCodes.SavePersonalInfo, info.ToBytes());

        // Send a reliable message to master server
        Connections.ClientToMaster.Peer.SendMessage(msg, DeliveryMethod.Reliable);
    }

    public static void GetPersonalInfo()
    {
        // Create an empty request message
        var msg = MessageHelper.Create(MyOpCodes.GetPersonalInfo);

        // Send message to master server, and wait for the response
        Connections.ClientToMaster.Peer.SendMessage(msg, (status, response) =>
        {
            // Response received

            if (status != AckResponseStatus.Success)
            {
                // If request failed
                Debug.LogError(msg.ToString());
                return;
            }

            // Success
            var info = response.DeserializePacket(new PersonalInfoPacket());

            Debug.Log(string.Format("I've got info. Name: {0}, Age: {1}", info.Name, info.Age));
        });
    }
```

Usually, when you want to retrieve something from the server, you make a request and expect a response. This is demonstrated in the `GetPersonalInfo` method. 

Sometimes, you might want to send a simple notification. Example of this can be found in `SavePersonalInfo` method. It sends a simple save request, and doesn't expect to get a response in return.

## Creating a Module Which Handles Requests

Your modules should extend `MasterModule` class. It will force you to implement method `Initialize`. This method will be called when master server starts, and all of the modules dependencies can be resolved.

In the example below, our module has one dependency, which is registered by calling `AddDependency<AuthModule>();`. AuthModule is not really used in the example, it's just there for demonstration purposes.

Adding dependencies with `AddDependency` will make sure that `Initialize` method is not called until master server initializes dependencies first.

``` C#

using Barebones.MasterServer;
using Barebones.Networking;
using UnityEngine;

/// <summary>
/// Our custom module
/// </summary>
public class MyModule : MasterModule
{
    private AuthModule _auth;
    private IMaster _master;

    void Awake()
    {
        // Register dependency. Initialize method will only be called
        // when modules in dependency list have been initialized
        AddDependency<AuthModule>();
    }

    /// <summary>
    /// Called, when all dependencies are met and master server is about to start
    /// </summary>
    public override void Initialize(IMaster master)
    {
        _master = master;
        _auth = master.GetModule<AuthModule>();
        
        // Add client message handlers,
        _master.AddClientHandler(new PacketHandler(MyOpCodes.GetPersonalInfo, HandleGetInfo));
        _master.AddClientHandler(new PacketHandler(MyOpCodes.SavePersonalInfo, HandleSaveInfo));

        // Listen to login event in the auth module
        _auth.OnLogin += OnLogin;

        Debug.Log("My module has been initialized");
    }

    protected virtual void OnLogin(ISession session, IAccountData data)
    {
        Debug.Log("MyModule was informed about a user who logged in");
    }

    private void HandleGetInfo(IIncommingMessage message)
    {
        var info = message.Peer.GetProperty(MyPropCodes.PersonalInfo) as PersonalInfoPacket;

        if (info == null)
        {
            // If there's no info
            message.Respond("You have no profile info", AckResponseStatus.Failed);
            return;
        }

        // We found the info
        message.Respond(info, AckResponseStatus.Success);
    }

    private void HandleSaveInfo(IIncommingMessage message)
    {
        // Deserialize packet
        var info = message.DeserializePacket(new PersonalInfoPacket());

        // Update the property value
        message.Peer.SetProperty(MyPropCodes.PersonalInfo, info);

        // Get the session (for no reason)
        var session = message.Peer.GetProperty(BmPropCodes.Session) as ISession;

        Debug.Log("Server saved personal info from user: " + session.Username);
    }
}
```

To handle a message from client, you'll need to add a handler by calling `master.AddClientHandler()`

When Master Server starts, it automatically "scans" the scene for all of the components that extend `MasterModule`. 

In general, it's convenient to add your modules as children of MasterServer component, like this:

![](http://i.imgur.com/r62rDYm.png)
