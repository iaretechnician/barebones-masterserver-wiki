## Module Info

* Main script: `ChatModule`
* Dependencies: **AuthModule** and **GamesModule** _(optional)_

## Adding Chat To Your Game

To add the chat functionality to your game, you'll need to:

1. Add **ChatModule** to the master server - without this module, master server will not be able to understand what kind of messages it's receiving from clients
2. Make a **Chat Terminal** on the client. By that I mean a simple view, which displays incoming messages and allows users to send them.

Adding the chat module to the master server is pretty simple - all you need is an empty game object with a `ChatModule` script, in the same scene as the Master Server

For the **chat terminal** - there's already one created for you as a prefab in `Barebones/MasterModules/Chat/Prefabs/Chat`. You can drag it into the `Canvas` (Unity UI) element in your scene.

If the need arises, you can use the API to create your own "chat view" from scratch.

## How "Chat View" Prefab Works

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

`public static void JoinChannel(string name, ChatCallback callback)`

`public static void LeaveChannel(string name, ChatCallback callback)`

`public static void GetChannels(Action<List<string>> callback)`

`public static void SendMessage(ChatMessagePacket packet, ChatCallback callback )`