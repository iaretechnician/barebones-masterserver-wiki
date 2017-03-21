⚠️ Not finished

**Player Profiles** module allows you to build player profiles, which are synchronized and persisted automatically.

When a player joins game server, game server can obtain players profile, make changes to it, and those changes will be sent to Master server.

![](http://i.imgur.com/pYZ9MhY.png)

:fire: It's important to remember that game server receives profile only once, and doesn't get any notifications about changes, made in master server (if you edit profile in master server while player is in game server, game server might override your changes). This is by design, because one-directional synchronization performs much better and allows a high level of optimizations. Bi-directional profile properties are planned to be released in the future.

## Main Principles Of Working With Profiles

In most cases, to use the profiles module, you will need to do 3 things:

1. Define profiles structure (profile factory) in **master server** 
1. (Optional) Get profile on client - it can be used to listen to changes to profile and display them on client's UI
1. (Optional) Get profile and modify it on game server - all the changes would be sent to master server, saved, and sent to client.

### Defining a Profile Structure in Master Server

Let's define keys for our properties first:

``` C#
public class MyProfileKeys
{
    public const short
        Coins = 0,
        Score = 1,
        Title = 2, 
        Inventory = 3;
}
```

You'll need to set the profiles factory on the `ProfilesModule`. You can do that simply by finding the profiles module in the scene:

``` C#
// Get the profiles module
var profilesModule = FindObjectOfType<ProfilesModule>();

// Set the profile factory
profilesModule.ProfileFactory = (username, peer) => new ObservableServerProfile(username)
{
    // Define all of the properties with default values
    new ObservableInt(MyProfileKeys.Coins, 10),
    new ObservableInt(MyProfileKeys.Score, 0),
    new ObservableString(MyProfileKeys.Title, "DefaultTitle"),
    new ObservableDictionary(MyProfileKeys.Inventory)
    // And so on, and on...
};

```

Now, if the factory is set successfully, when users logs into the game:

* Default profile will be created for each user (by calling the factory method)
* Master serer will check the database to restore previous values

## Listening to Profile Changes On Client

Client will need to create the profile, and send a request to master server, to fill it's values.

Client doesn't really know what kind of profile was created for him on the master server, and he **doesn't need to** - he only needs to add properties that interest him.

:warning: By default, if profiles don't match, an error will be logged to notify you about it (in case you did it accidentally). You can disable the error by checking `ProfilesModule.IgnoreProfileMissmatchError` in your code or in the inspector.

``` C#
// Create a profile (we're intentionally not constructing all properties)
var profile = new ObservableProfile()
{
    new ObservableInt(MyProfileKeys.Coins, 5),
    new ObservableString(MyProfileKeys.Title, "DefaultTitle"),
};

// Send a request to master server, to fill profile values
Msf.Client.Profiles.GetProfileValues(profile, (isSuccessful, profileError) =>
{
    if (!isSuccessful)
    {
        Logs.Error(profileError);
        return;
    }

    // Listen to property updates
    profile.PropertyUpdated += (code, property) =>
    {
        // Log a message, when property changes
        Logs.Info("Property changed:" + code + " - " + property.SerializeToString());
    };

});
```

Or you can listen to profile changes directly:

``` C#
// Listen directly to changes in coins property
var coinsProp = profile.GetProperty<ObservableInt>(MyProfileKeys.Coins);
coinsProp.OnDirty += property =>
{
    Logs.Info("Coins changed to: " + coinsProp.Value);

    // OR
    // Logs.Info("Coins changed to: " + (property as ObservableInt).Value);
};
```

## Retrieving And Changing Player Profile In Game Server

Game server retrieves profile data almost identically as client does. Main difference is that instead of `ObservableProfile` it creates `ObservableServerProfile`, which requires player's username.

To retrieve data, server calls `Msf.Server.Profiles.FillProfileValues`

``` C#
var username = "playerUsername";

// Construct the profile
var profile = new ObservableServerProfile(username)
{
    new ObservableInt(MyProfileKeys.Coins, 5),
    new ObservableString(MyProfileKeys.Title, "DefaultTitle"),
};

// Fill profile values
Msf.Server.Profiles.FillProfileValues(profile, (successful, error) =>
{
    if (!successful)
    {
        Logs.Error(error);
        return;
    }

    // Modify profile properties (changes will automatically be sent to the master server, 
    // and then to client)
    profile.GetProperty<ObservableInt>(MyProfileKeys.Coins).Add(4);
    profile.GetProperty<ObservableString>(MyProfileKeys.Title).Set("DifferentTitle");

});
```

## Observable Properties

You might have noticed that profile is built using special observable properties that implement `IObservableProperty` interface. This interface contains methods that are required for syncing your special values.

You can add properties to profile by calling:
* `profile.AddProperty(IObservableProperty)`

There are two methods which you can use to see get properties of the profile:
* `profile.GetProperty(key)` - returns the value of type `IObservableProperty`
* `profile.GetProperty<Type>(key)` - generic method which returns the value of type `Type`

Here's how you retrieve a property of `ObservableInt` type: 

``` C#
var coinsProperty = profile.GetProperty<ObservableInt>(MyProfileKeys.Coins);
var coins = coinsProperty.Value;
```

## Built-in Observable Types

Here's a list of built-int observable types and some of their methods.
* `ObservableInt`
  * `.Value` - property, which returns the actual underlying integer value
  * `.Set(value)` - sets the value
  * `.Add(value)` - adds to the current value. You can subtract by providing a negative number
  * `.TryTake(amount)` - if provided amount is smaller than the value, returns true, and reduces the value by ammount
* `ObservableFloat`
  * `.Value` - property, which returns the actual underlying float value
  * `.Set(value)` - sets the value
  * `.Add(value)` - adds to the current value. You can subtract by providing a negative number
  * `.TryTake(amount)` - if provided amount is smaller than the value, returns true, and reduces the value by ammount
* `ObservableString`
  * `.Value` - property, which returns the actual underlying string value
  * `.Set(value)` - sets the value
* `ObservableDictionary` - represents a `Dictionary<string, string>`
  * `.Values` - property, which returns a **immutable** list of string values
  * `.Pairs` - property, which returns an **immutable** list of KeyValuePair's of the dictionary
  * `.UnderlyingDictionary` - property, which returns an underlying dictionary. **This is mutable!**
  * `.SetValue(key, value)` - sets a value in the dictionary (updates an existing, or adds a new one)
  * `.Remove(key)` - removes a value from dictionary
* `ObservableDictionaryInt` - represents a `Dictionary<int, int>`
  * `.Values` - property, which returns a **immutable** list of intvalues
  * `.Pairs` - property, which returns an **immutable** list of KeyValuePair's of the dictionary
  * `.UnderlyingDictionary` - property, which returns an underlying dictionary. **This is mutable!**
  * `.SetValue(key, value)` - sets a value in the dictionary (updates an existing, or adds a new one)
  * `.Remove(key)` - removes a value from dictionary
* `ObservableDictionaryStringInt` - represents a `Dictionary<string, int>`
* `ObservableDictionaryStringFloat` - represents a `Dictionary<string, float>`

## Creating Your Own Observable types

You might find yourself in a situation where none of the observables that come with the asset suit your needs. You can write your own observable implementations by implementing interface `IObservableProperty` or extending `ObservableBase` class.
