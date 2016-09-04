This section will cover some of the basics of networking with Barebones Networking API.

### What's The Barebones Networking API?
It's a layer of abstraction on top of networking technologies / protocols, that simplifies communication between servers and clients. It's designed to be fast, convenient and very extendable - you can fine tune structure of your messages, change communication protocols, and none of your networking code would have to change.

### Supported Protocols
At the moment of writing this, two protocols are supported.
* **Websockets/TCP** - based on websocket-sharp
* **UDP/RUDP** - based on unity's Network Transport Layer

They should cover pretty much every use case. If there's something else you need, feel free to post a request.

## General Overview
To establish a connection between two endpoints, one of them must be a **client** (`IClientSocket`), and another - a **server** (`IServerSocket`). 

When connection is established, both server and client will see each other as peers (`IPeer`). Each one of them can send message (`IMessage`) and receive them (`IIncommingMessage`).

## Starting a Server Socket 
Server sockets implemet `IServerSocket` interface. It exposes two events and two methods
* `OnConnected` (event) - invoked, when client connects to this socket. `IPeer` instance, which represents a connected client, will be provided as an argument
* `OnDisconnect` (event) - invoked, when client disconnects
* `Listen(port)` - opens the socket and starts listening to the port. After calling this method, clients can start connecting
* `Stop()` - stops listening

``` C#
    private void StartServer()
    {
        // Create a server socket
        IServerSocket server = new ServerSocketUnet();

        server.OnConnected += peer =>
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
* `OnConnected` (event) - Invoked on successful connection
* `OnDisconnected` (event) - Invoked when disconnected from server socket
* `OnStatusChange` (event) - Invoked when connection status changes
* `Connect(ip, port)` - Starts connecting to server socket at given address
* `Disconnect()` - Closes the connection
* `WaitConnection(callback)` - a helpful method, which will invoke a callback when connection is established, or after a failed attempt to connect. If already connected - callback will be invoked istantly.
* `AddHandler(handler)` - adds a message handler of a specific operation code. If there's already a handler with same op code, it will be overriden.

``` C#
    private void StartClient()
    {
        // Create a server socket
        IClientSocket client = new ClientSocketUnet();

        client.OnConnected += () =>
        {
            Debug.Log("Client: I've connected to server");
        };

        client.Connect("127.0.0.1", 777);
    }
```
 
## Exchanging Messages

Client and server communicate to each other through `IPeer` interface. 
Server will get clients peer when it connects, and client can access servers peer object through `IClientSocket.Peer`

### Client to Server
To receive messages, server will need to listen to the event on `IPeer`:

``` C#
        server.OnConnected += peer =>
        {
            // Client just connected
            peer.OnMessage += message =>
            {
                // Handle peer messages
                Debug.Log("Server: I've got a message from client: " + message.AsString());
            };
        };
```

Client can send a message like this:

``` C#
        client.OnConnected += () =>
        {
            var msg = MessageHelper.FromString(0, "Yo!");
            client.Peer.SendMessage(msg, DeliveryMethod.Reliable);
        };
```

### Server to Client

You can do it pretty much the same way you sent messages from client to server. 

Server code:
``` C#
        server.OnConnected += peer =>
        {
            // Client just connected
            var msg = MessageHelper.FromString(0, "Sup?");
            peer.SendMessage(msg, DeliveryMethod.Reliable);
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
        client.AddHandler(new PacketHandler(0, message =>
        {
            Debug.Log("I've got the message!: " + message.AsString());
        }));

        client.OnConnected += () =>
        {
        };
```
