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

** `Peer` - peer, to which client has connected. This is what you'd use to send messages to server.
** `Status` - Status of the connection
** `IsConnected` - Returns true, if we are connected to another socket
** `IsConnecting` - Returns true, if we're in the process of connecting
** `OnConnected` (event) - Invoked on successful connection
** `OnDisconnected` (event) - Invoked when disconnected from server socket



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
 