**Player Profiles** module allows you to build player profiles, which are synchronized and persisted automatically.

When a player joins game server, game server can obtain players profile, make changes to it, and those changes will be sent to Master server.

![](http://i.imgur.com/pYZ9MhY.png)

:fire: It's important to remember that game server receives profile only once, and doesn't get any notifications about changes, made in master server (if you edit profile in master server while player is in game server, game server might override your changes). This is by design, because one-directional synchronization performs much better and allows a high level of optimizations. Bi-directional profile properties are planned to be released in the near future.

## Defining a Profile Structure

In most cases, you'll need your profiles to be constructed the same way, in all servers and clients. To do that, you'll need to call `ProfilesModule.SetFactory` method, and provide a parameter with your construction method. 

Here's an example of how you can create your own profile factory. This component should be added to all of your scenes (or at least the ones, where client and servers are using profiles)

``` C#
using UnityEngine;
using Barebones.MasterServer;

public class MyGame : MonoBehaviour {

	// Use this for initialization
	void Awake () {

        // Set the factory method
	    ProfilesModule.SetFactory(ProfileFactory);
	}

    public static ObservableProfile ProfileFactory(string username)
    {
        var profile = new ObservableProfile(username);

        // Registering profile variables
        profile.AddProperty(new ObservableInt(MyKeys.Coins, 1000));
        profile.AddProperty(new ObservableString(MyKeys.Weapon, "Sword"));
        profile.AddProperty(new ObservableDictionaryInt(MyKeys.Inventory));

        return profile;
    }

    /// <summary>
    /// Keys of profile properties
    /// </summary>
    public static class MyKeys
    {
        public const short Coins = 0;
        public const short Weapon = 1;
        public const short Inventory = 2;
    }
}
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
        var coinsProperty = profile.GetProperty<ObservableInt>(MyKeys.Coins);
        var oldValue = coinsProperty.Value;
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

## Creating Your Own Observable types

You might find yourself in a situation where none of the observables that come with the asset suit your needs. You can write your own observable implementations by implementing interface `IObservableProperty` or extending `ObservableBase` class.