## Module Info

* Main script: `ChatModule`
* Dependencies: **AuthModule** and **GamesModule** _(optional)_

## Adding Chat To Your Game

To add the chat functionality to your game, you'll need to:

1. Add **ChatModule** to the master server - without this module, master server will not be able to understand what kind of messages it's receiving from clients
2. Make a **Chat Terminal** on the client. By that I mean a simple view, which displays incoming messages and allows users to send them.

Adding the chat module to the master server is pretty simple - all you need is an empty game object with a `ChatModule` script, in the same scene as the Master Server

ℹ️ For the **chat terminal** - there's already one created for you as a prefab in `Barebones/MasterModules/Chat/Prefabs/Chat`. You can drag it into the `Canvas` (Unity UI) element in your scene.

ℹ️ Most of the **ChatView** methods are virtual so you can override them.

If the need arises, you can use the API to create your own "chat view" from scratch.

## Chat View Prefab

If you decide to use the chat view prefab, here's a list of commands you can use:

* Send private messages with command: `/w username message`
* Reply to the person who last sent you a message: `/r message`
* Join / Create channel: `/join channelName` (if channel doesn't exist, it will be created)
* Leave channel: `/leave channelName`
* Write in channel: `/c message`, `/ch message`, `/csay message`
* Get a list of channels we're in: `/c`

ℹ️ These commands are parsed in the View script on the client, and used to create the `ChatMessagePacket` instance, which represents the actual message. In other words, these commands are only **client-specific**

ChatView has two special properties:

* **AutoJoinChannels** - it's a list of channel names, to which client will connect automatically
* **ChannelMasks** - a list of strings. If client sends or receives a message from a channel with name contains at least one of the words in the list, channel name will be masked.

Main use for channel masks is to hide names of "Local Channels" that are generated. Consider the use case where you want each game to have a unique channel, to which users can write "locally".

1. Chat Module gets a notification saying that client joined a game with id 10
1. Chat Module creates a special channel or finds an existing one with name `Game-10`
1. User who joined the game is added to that channel, and it's set as "local" in the players peer properties
1. When client sends a channel message with an empty string for a channel - chat module interprets it as a local message, and sends to the client.
1. Client receives a message from channel `Game-10`.
1. Chat View sees that a message came from a channel which has a word `Game-` (this word is added to the ChannelMasks property of the view),
1. Chat View masks the channel, and the message is displayed as not in a channel.

## Inner Workings

### Sending Chat Messages

Chat messages are instances of `ChatMessagePacket`. It contains these properties:

* `byte Type` - determines the type of message sent (for example, a private message or a channel message)
* `string Receiver` - receiver of the message, which will depend on the type. For example, if sending a private message, receiver will be username of the account you are sending the message to. If it's a channel message - receiver should be the name of the channel.
* `string Message` - contents of the message
* `string Sender` - username of the sender. When sending a message from client, you can leave this empty, and it will be set automatically in the server

### Message Types

At the moment of writing, chat module supports two types of messages:

* **Private Message** - a message sent to a connected user
* **Channel Message** - a message sent to a specific channel and broadcast to its members

#### Local Message

If you send a channel message with empty string as channels name, message will be considered a to be a **Local Message**. 

When master server receives such message, it will check senders **peer properties** to check if he has a local channel, like this:

`var channel = sender.Peer.GetProperty(BmPropCodes.LocalChatChannel) as ChatChannel;`

If the channel is found in the property, message will be broadcasted to that channel.

### Receiving Chat Messages

Chat messages will be pushed to client. To listen to them, client can simply add a handler to the connection:

``` C#

public virtual void Start () {

    // Create a handler
    var handler = new PacketHandler(BmOpCodes.ChatMessage, HandleMessage);

    // Add a handler to listen to incomming chat messages
    Connections.ClientToMaster.AddHandler(handler);
}

protected virtual void HandleMessage(IIncommingMessage message) {
    // Deserialize the message packet
    var packet = message.DeserializePacket(new ChatMessagePacket());

    Debug.Log("Message received:"+ packet.Message);
}

```

Created messages can be send by invoking the static method `ChatModule.SendMessage`


``` C#
    protected virtual void SendLocalMessage(string message)
    {
        var packet = new ChatMessagePacket()
        {
            Message = message,
            Receiver = "",
            Type = ChatMessagePacket.ChannelMessage
        };

        ChatModule.SendMessage(packet, (successful, error) =>
        {
            if (!successful)
            {
                Debug.LogError(error);
            }
        });
    }
```

## API

Static methods can be accessed through `ChatModule` class, for example `ChatModule.JoinChannel(..)`

`public static void JoinChannel(string name, ChatCallback callback)`

`public static void LeaveChannel(string name, ChatCallback callback)`

`public static void GetChannels(Action<List<string>> callback)`

`public static void SendMessage(ChatMessagePacket packet, ChatCallback callback )`