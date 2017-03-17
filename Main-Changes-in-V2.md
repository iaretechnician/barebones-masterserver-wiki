This list does not cover all of the changes that were made - only the most important ones.

## API

One of the most important changes was the introduction of `Msf` class. API of the modules, that come with the framework, will be exposed through this class. Most of the default functionality was split into two properties:

* `Msf.Client` - functionality, which is inteded to be used by clients
* `Msf.Server` - functionality, which is intended to be used by servers

> **Advanced usage** - most of the module functionality, exposed through API, allows you to make requests to a given connection, or changing the default connection. For example, you can have Chat Module running on another server, and on client do something like `Msf.Client.Chat.ChangeConnection(connectionToAnotherServer)`. This way, you can offload chat traffic to another server, and still use the chat API like you normally would.

## Changes to Master Server

**Single port usage** - from now on, master server will open only a **single port**, to which everything will connect (in V1, Master Server used to require 3 ports to be open - one for game server, one for clients, and one for spawners)

**Abstracted concept of servers** - in V2, I've introduced `ServerBehaviour` object. It acts almost like Master Server did in V1.x (supports modules and etc). This allows you to have setups with multiple servers, and even splitting some of the functionality to other servers.

## Easier Messaging

You no longer need to create message objects, which was a bit tedious. Now, you can send packets and other serializable data in-line, like this:

``` C#
// Sending a Unet messages (instances of MessageBase) to server
Msf.Connection.SendMessage(777, new IntegerMessage(5));

// Sending a string message to server, and expecting for response
Msf.Connection.SendMessage(777, "My message", (status, response) =>
{
    if (status == ResponseStatus.Success)
    {
        Logs.Error("Request was successful!");
    }
});
```

## Game Servers Will Now Be "Game Rooms"

I've added an ability to register multiple game rooms from within one Peer (and one unity instance). 

This should be useful for games that don't need physics, and are not traffic-intensive (card games, 1v1 games and etc.).  For these types of games, you can setup your game server to run multiple rooms on a single Unity process.

## Cleaned Up Unnecessary And Confusing Names

A bunch of classes were renamed and restructure, to better represent their use. 

Now, the official abbreviation for framework is **MSF**, and all of the command line arguments will start with "-msf..."

## Reduced coupling between modules

**Removed dependencies on Auth module**

**Optional Dependencies** - Modules can now have optional dependencies (for example: `AddOptionalDependency<ProfilesModule>();`)
