### IPeer

In master server (or any other server, based on Networking API), every connected client is represented by `IPeer` implementation.

For example, when server receives a message, you can access the peer who sent it.

``` C#
protected virtual void HandleSomeMessage(IIncommingMessage message)
{
    var peer = message.Peer;
}
```

### State

Quite often, you will want to save some stateful information, and there two ways to do it.

* Peer **Properties** - they are `object`'s, stored by property ID (`int`)
* Peer **Extensions** - stored by object `Type`

You should use properties when you know that you're going to have multiple objects of same type (you can't have same type  extensions)

Main benefit of using extensions is that you don't need to manage property id's, and you can also use store extensions as interface implementations. For example, `AuthModule` stores an implementation of `IUserExtension`, so you can access it like this:

``` C#
// Get the extension from peer
var user = peer.GetExtension<IUserExtension>()
```

This way, if you don't want to use the default `AuthModule`, you can write your own, store the extension, and all of the functionality that depends on `IUserExtension` will remain unchanged:

``` C#
/// <summary>
/// Custom user implementation
/// </summary>
public class CustomUserExtension : IUserExtension
{
    public string Username { get; private set; }

    public CustomUserExtension(string username)
    {
        Username = username;
    }

    /** Interface implementation 
        * ...
        * 
        * **/
}

/// <summary>
/// Custom Login request handler
/// </summary>
protected virtual void HandleCustomLoginRequest(IIncommingMessage message)
{
    if (message.Peer.HasExtension<IUserExtension>())
    {
        message.Respond("You're already logged in", ResponseStatus.Unauthorized);
        return;
    }

    var newUser = new CustomUserExtension("Some username");

    // Set the peer extensions
    message.Peer.AddExtension<IUserExtension>(newUser);
}
```

#### Peer Properties Example

``` C#
// ---------------------------
// Set property
peer.SetProperty(5, "some kind of data");

// Get property
var propertyValue = peer.GetProperty(5) as string;
```

#### Peer Extension Example

``` C#
public class CustomExtension
{
    public int SomeValue;
    public string SomeOtherValue;
}

protected virtual void HandleSomeMessage(IIncommingMessage message)
{
    var customExtension = new CustomExtension();

    // Set the extension
    message.Peer.AddExtension(customExtension);

    // Get the extension
    var ext = message.Peer.GetExtension<CustomExtension>();

}
```