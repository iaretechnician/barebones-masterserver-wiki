### Picking a Username

Users have to identify themselves to Chat module, so that chat module knows where to send messages.

⚠️ If `ChatModule.UseAuthModule` is set to true, logged in users will automatically be identified to chat module.

``` C#
// Pick a username for the chat
Msf.Client.Chat.PickUsername("MyUsername", (successful, error) =>
{
    if (!successful) Logs.Error(error);
});
```

### Joining and Leaving a channel

``` C#
// Join a channel
Msf.Client.Chat.JoinChannel("ChannelName", (successful, error) => { });

// Leave a channel
Msf.Client.Chat.LeaveChannel("ChannelName", (successful, error) => { });
```

### Getting a list of channels a user has joined

``` C#
// Get a list of channels a user has joined
Msf.Client.Chat.GetMyChannels((channels, error) =>
{
    if (channels == null)
    {
        Debug.LogError(error);
        return;
    }

    Debug.Log("User channels: " + string.Join(", ", channels.ToArray()));
});
```

### Getting a list of users in the channel

``` C#
// Get a list of users in a specific channel
Msf.Client.Chat.GetUsersInChannel("ChannelName", (users, error) =>
{
    if (users == null)
    {
        Debug.LogError(error);
        return;
    }

    Debug.Log("Users in channel: " + string.Join(", ", users.ToArray()));
});
```

### Setting a Default Channel

``` C#
// Sets a default chat channel (if no channel name is given when sending a message,
// this message will be sent to default channel)
Msf.Client.Chat.SetDefaultChannel("ChannelName", (successful, error) => { });
```

### Sending Messages

``` C#
// Send a message to default channel
Msf.Client.Chat.SendToDefaultChannel("This is a message", (successful, error) => { });

// Send a private message
Msf.Client.Chat.SendPrivateMessage("ReceiverUsername", "your message", (successful, error) => { });

// Send a message to a specific channel
Msf.Client.Chat.SendChannelMessage("ChannelName", "message", (successful, error) => { });
```

### Handling / Receiving Chat Messages

Client can listen to these events:

* `Msf.Client.Chat.MessageReceived`
* `Msf.Client.Chat.UserJoinedChannel`
* `Msf.Client.Chat.UserLeftChannel`

This is an example of a script which listens to incoming messages and events:

``` C#

using Barebones.MasterServer;
using UnityEngine;

public class ChatApiExample : MonoBehaviour
{
    void Awake()
    {
        // Subscribe to events
        Msf.Client.Chat.MessageReceived += OnMessageReceived;
        Msf.Client.Chat.UserJoinedChannel += OnUserJoinedChannel;
        Msf.Client.Chat.UserLeftChannel += OnUserLeftChannel;
    }

    private void OnUserLeftChannel(string channel, string user)
    {
        Debug.Log(string.Format("[{0}] User '{1}' has left", channel, user));
    }

    private void OnUserJoinedChannel(string channel, string user)
    {
        Debug.Log(string.Format("[{0}] User '{1}' has joined", channel, user));
    }

    private void OnMessageReceived(ChatMessagePacket message)
    {
        switch (message.Type)
        {
            case ChatMessagePacket.PrivateMessage:
                // Received a private message
                Debug.Log(string.Format("From [{0}]: {1}",
                    message.Sender, // Channel name
                    message.Message));
                break;

            case ChatMessagePacket.ChannelMessage:
                // Received a channel message
                Debug.Log(string.Format("[{0}] [{1}]: {2}", 
                    message.Receiver, // Channel name
                    message.Sender,
                    message.Message));
                break;
        }

    }

    void OnDestroy()
    {
        // Unsubscribe from events
        Msf.Client.Chat.MessageReceived -= OnMessageReceived;
        Msf.Client.Chat.UserJoinedChannel -= OnUserJoinedChannel;
        Msf.Client.Chat.UserLeftChannel -= OnUserLeftChannel;
    }
}

```