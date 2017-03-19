### What is The Barebones Networking API?
It's a layer of abstraction on top of networking technologies / protocols, that simplifies communication between servers and clients. It's designed to be fast, convenient and very extendable - you can fine tune structure of your messages, change communication protocols, and none of your networking code would have to change.

### Supported Protocols
At the moment of writing this, two protocols are supported.
* **Websockets/TCP** - based on websocket-sharp. Main classes: `ClientSocketWs`, `ServerSocketWs`
* **UDP/RUDP** - based on unity's Network Transport Layer. Main classes: `ClientSocketUnet`, `ServerSocketUnet`

They should cover pretty much every use case. If there's something else you need, feel free to post a request.

## General Overview
To establish a connection between two endpoints, one of them must be a **client** (`IClientSocket`), and another - a **server** (`IServerSocket`). 

When connection is established, both server and client will see each other as peers (`IPeer`). Each one of them can send message (`IMessage`) and receive them (`IIncommingMessage`).

## Starting a Server Socket 
Server sockets implemet `IServerSocket` interface. It exposes two events and two methods
* `Connected` (event) - invoked, when client connects to this socket. `IPeer` instance, which represents a connected client, will be provided as an argument
* `Disconnected` (event) - invoked, when client disconnects
* `Listen(port)` - opens the socket and starts listening to the port. After calling this method, clients can start connecting
* `Stop()` - stops listening

``` C#
    private void StartServer()
    {
        // Create a server socket
        IServerSocket server = new ServerSocketUnet();

        server.Connected += peer =>
        {
            Debug.Log("Server: client connected: " + peer.Id);
        };

        // Start listening
        server.Listen(777);
    }
```

## Connecting With a Client
Client socket implements `IClientSocket` interface. Exposed properties and methods are as follow:

* `Peer` - peer, to which client has connected. This is what you'd use to send messages to server.
* `Status` - Status of the connection
* `IsConnected` - Returns true, if we are connected to another socket
* `IsConnecting` - Returns true, if we're in the process of connecting
* `Connected` (event) - Invoked on successful connection
* `Disconnected` (event) - Invoked when disconnected from server socket
* `StatusChanged` (event) - Invoked when connection status changes
* `Connect(ip, port)` - Starts connecting to server socket at given address
* `Disconnect()` - Closes the connection
* `WaitConnection(callback)` - a helpful method, which will invoke a callback when connection is established, or after a failed attempt to connect. If already connected - callback will be invoked istantly.
* `AddHandler(handler)` - adds a message handler of a specific operation code. If there's already a handler with same op code, it will be overriden.

``` C#
    private void StartClient()
    {
        // Create a server socket
        IClientSocket client = new ClientSocketUnet();

        client.Connected += () =>
        {
            Debug.Log("Client: I've connected to server");
        };

        client.Connect("127.0.0.1", 777);
    }
```
 
## Exchanging Messages

Client and server communicate to each other through `IPeer` interface. 
Server will get clients peer when it connects, and client can access servers peer object through `IClientSocket.Peer`

There are many overload methods for sending and responding to messages. You can check them by opening `IMsgDispatcher` and `IIncommingMessage` interfaces. Examples below will show you some of the basic methods you can use and how to use them.

### Client to Server
To receive messages, server will need to listen to the event on `IPeer`:

``` C#
        server.Connected += peer =>
        {
            // Client just connected
            peer.OnMessage += message =>
            {
                // Handle peer messages
                Debug.Log("Server: I've got a message from client: " + message.AsString());
            };
        };
```

Client can send a simple string message like this:

``` C#
        client.Connected += () =>
        {
            client.Peer.SendMessage(0, "Hey!");
        };
```

### Server to Client

You can do it pretty much the same way you sent messages from client to server. 

Server code:
``` C#
        server.Connected += peer =>
        {
            // Client just connected
            peer.SendMessage(0, "What's up?");
        };
```

Client code:

``` C#
        client.OnConnected += () =>
        {
            client.Peer.OnMessage += message =>
            {
                Debug.Log("I've got the message!: " + message.AsString());
            };
        };
```

However, it's not the only way for client to handle a message. You can add a handler which would handle a message with specific op code, such as:

``` C#
        client.SetHandler(0, message =>
        {
            Debug.Log("I've got the message!: " + message.AsString());
        });

        client.Connected += () =>
        {
        };
```

### Responding to Messages

If you want to send a message and get something in return, a.k.a send a request and get a response, there's a useful overload method for that.

**Server:** 
``` C#
        server.Connected += peer =>
        {
            // Send a message to client
            peer.SendMessage(0, "What's up?", (status, response) =>
            {
                // This get's called when client responds
                if (status == ResponseStatus.Success)
                {
                    Debug.Log("Client responded: " + response.AsString());
                }
            });
        };
```

**Client:**
``` C#
        client.SetHandler(0, message =>
        {
            // Message received, let's respond to it
            message.Respond("Not much", ResponseStatus.Success);
        });
```

## Creating Messages
Messages should be created through `MessageHelper`. It holds factory methods that construct actual message objects that implement `IMessage` interface. When sent, every message get's converted into byte array.

MessageHelper has these basic methods for creating messages out of some common types:
* `Create(opCode)` - creates an empty message
* `Create(opCode, byte[])` - creates a message with data provided
* `Create(opCode, string)` - creates a message from string
* `Create(opCode, int)` - creates a message from integer 

When receiver handles this message and receives an `IIncommingMessage`, it can use these methods to conveniently transform message to a type it was sent in:
* `AsBytes()` - returns your data in byte[] array
* `AsString()` - uses data in message to convert it to string
* `AsInt()` - uses data in message to convert it to int

## Serialization
Every single peace of data you send is converted into `byte[]`

To avoid reflection and AOT methods, I didn't use any third party serialization libraries. Instead, every packet is serialized manually - it's not difficult to do, and gives you full control.

:information_source: It doesn't mean you can't use serialization libs, such as **Json.NET** or **protobuf-net**. As long as they can turn objects into bytes and back again, and your platform supports them - have fun!

There are some helpful extension methods for turning common types to byte array and back:

* `String.ToBytes()` - use it like this: `"my string".ToBytes()`
* `Dictionary<string,string>.ToBytes()`
* `Dictionary<string,string>.FromBytes(byte[])` - use it like this: `new Dictionary<string, string>().FromBytes(data)`
* `Dictionary<int,int>.ToBytes()`
* `Dictionary<int,int>.FromBytes(byte[])`

You can extend `SerializablePacket` to create custom packet classes, like this:

``` C#
using Barebones.Networking;

namespace Barebones.MasterServer
{
    public class GameAccessPacket : SerializablePacket
    {
        public string AccessToken;
        public string Address;
        public string SceneName = "";

        public override void ToBinaryWriter(EndianBinaryWriter writer)
        {
            writer.Write(AccessToken);
            writer.Write(Address);
            writer.Write(SceneName);
        }

        public override void FromBinaryReader(EndianBinaryReader reader)
        {
            AccessToken = reader.ReadString();
            Address = reader.ReadString();
            SceneName = reader.ReadString();
        }
    }
}
```

If you're using `SerializablePacket`, here's how you serialize, send, receive and deserialize. Sending a message:

``` C#
            // Create a packet
            var packet = new GameAccessPacket()
            {
                AccessToken = "54fgf4wr81wx85nh5io5gh",
                Address = "127.0.0.1",
                SceneName = "Main"
            };

            // Create a message from the packet
            var msg = MessageHelper.Create(0, packet.ToBytes());

            // Send the message
            client.Peer.SendMessage(msg, DeliveryMethod.Reliable);
```

Receive a packet:

``` C#
            server.OnConnected += peer =>
            {
                peer.OnMessage += message =>
                {
                    // We received a message
                    var data = message.DeserializePacket(new GameAccessPacket());
                    Debug.Log(data.AccessToken);
                };
            };
```

