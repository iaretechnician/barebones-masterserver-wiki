This page will guide you through the steps you can take to start working on your game and back-end server.

⚠️ 🔴 Make sure to build your game so that it can **run in the background** - it's necessary for any game that needs networking. Otherwise, you might experience some nasty glitches. You'll find the setting in **File > Build Settings > Player Settings > Run In Background**

⚠️ I accidentally published the version where game scene name is set to "AwesomeGame". Change it in Canvas > Create Game Window

![](http://i.imgur.com/Y46sjiy.png)

## Preparations

1. Create a new folder in your project asset's root, and name it **MyGame**
1. Create two more folders within MyGame, called **Scripts** and **Scenes**

:warning: If there's already a folder named **MyGame** in the `Barebones/Demos` folder, you can delete it. It contains the outcome of this tutorial.

![](http://i.imgur.com/I6iep44.png)

# Setting Up Main Screen 

To make things faster, a pre-made scene is provided within the asset, and it's located at `Barebones/MasterFramework/Scenes -> MainEmpty`. When you work on your game, you're not forced to use this scene, but it's very convenient to make a copy of it and customize it the way you want.

Open the scene, hit `File > Save Scene As`, name it **MyMain** and save it to `MyGame/Scenes`

If you hit "Play" in the editor, you should see something like this at the bottom right:

![](http://i.imgur.com/RJnPvIV.png)

It means that Master and Spawner servers are started, and client (you) is connected to master server. You might be wondering "Why and how these servers are started?" Take a look at the hierarchy:

![](http://i.imgur.com/9uEm8vU.png)

Here's what these objects are there for:

* Barebones - object, used to conveniently separate framework related stuff from the level
* Connection - this object contains a component which connects client to Master Server
* MasterServer - this is a component, which starts the master server. It has an **Auto Start In Editor** option, which is why it starts automatically when we hit a "play" button in the editor.
* GameServerStarter - contains a component, which starts a game server. It also contains an **Auto Start** option, but it should be **Not Selected** in the main screen. Technically, in the editor, when in main screen, it does pretty much nothing. Only when you start server through command line, it picks up necessary arguments provided, and starts a game server.
* SpawnerServer - this script starts a server, which is responsible for spawning new game processes
* ClientConnector - takes care of connecting a player to game server. Depending on your game-joining flow, you might need to write your own connector

:information_source: If you tried to log in , you probably got an error saying that there's no profile factory. We'll fix this in the next step

# Profiles Factory (Optional) / Shared Class 

When your clients log in to master server, profiles module will try to construct a profile for each of them. Which is more, clients should also be able to construct their own profile, so that they can subscribe to various observable properties.

This means that client and server should share the same logic for creating profiles. Create a new script called **MyGameShared** in the Scripts folder. and paste the code below:

``` C#
using UnityEngine;
using Barebones.MasterServer;

public class MyGameShared : MonoBehaviour {

	void Awake () {
        // Let the module know which factory to use
        ProfilesModule.SetFactory(ProfileFactory);
    }

    public static ObservableProfile ProfileFactory(string username)
    {
        // Create a profile and add coins property to it
        var profile = new ObservableProfile(username);
        profile.AddProperty(new ObservableInt(MyProfileKeys.Coins, 10));
        return profile;
    }
}

public class MyProfileKeys
{
    public const int Coins = 0;
}
```

This component simply sets a profiles factory (which is a simple method) when it's awakened. Our profile factory simply adds one property, called coins, and sets its default value to 10. Check out player profiles section of the documentation for more info on the matter.

This script should be added to your main screen and, later, to every other screen of your game (mainly to make debugging easier). In this example, it's added to component named **Shared**

![](http://i.imgur.com/UU5q115.png)

# Game Scene Setup

To guide you through game scene setup, we'll be using a small game, made with uNET, called **MiniGame**. You can find it at `Barebones/Demos/MiniGame`. 

**MiniGame** represents a regular uNET game, where you host server and connect to it via NetworkManagerHUD.

:information_source: If you're not familiar with uNET, I recommend you go through a tutorial in the [official unity's manual](https://docs.unity3d.com/Manual/UNetSetup.html)

### Overview

General steps to setting up a game scene are as follows:

1. Add game server object (**IGameServer**) - it should handle receiving passes from clients, disconnecting players that are not allowed and etc.
1. Add server **starter script** - this should be a script that reads command line arguments and starts the game server accordingly (example: `GameServerStarter.cs`)
1. Add **connector script** - a script that connects client to game server, when client opens the scene. (example: `UnetConnector.cs`)
1. Add **MasterServer** prefab - this is not necessary, but recommended for faster development. It will start master server automatically, so that you don't need to.

### Copy the Game Scene

Open the **MiniGame** scene, hit `File > Save Scene As`, and Save to to your scenes as you did with the main scene. In this example, we'll call it **MyGame**

### Implementing IGameServer interface

Our game server is only valid when it implements `IGameServer `interface. If you're using uNET HLAPI, general implementation is already written for you in script **`UnetGameServer.cs`**, this is what we'll be using to set things up faster. 

1. Create a new script in `MyGame/Scripts`, and call it **MyGameServer.cs**

2. Make sure your newly created class extends `UnetGameServer`. To do that, you'll need to implement two methods: `OnServerUserJoined` and `OnServerUserLeft`. These methods are called when authenticated user joins and leaves a game.

3. Call `MiniNetworkManager.SpawnPlayer(client.Connection, client.Username, "knife")` in the `OnServerUserJoined`. As you might have guessed, this will spawn player's character when player successfully joins the game. OnServerUserJoined is called when the player has officially joined the game. 

    Here's what your script should look like:

    ```C#
using Barebones.MasterServer;

public class MyGameServer : UnetGameServer {

    /// <summary>
    /// Called, when authenticated user sends a valid access token
    /// </summary>
    /// <param name="client"></param>
    protected override void OnServerUserJoined(UnetClient client)
    {
        MiniNetworkManager.SpawnPlayer(client.Connection, client.Username, "knife");
    }

    protected override void OnServerUserLeft(UnetClient client)
    {
    }
}

    ```
4. Create a new object in the root of the hierarchy and name it **Networking**
5. Create a new empty game object as a child of **Networking **object and call it **GameServer**
6. Attach a newly created component to it.
7. Add the **MyGameShared** component (the one you created earlier) to the scene as you did in the main scene.
    
    ![](http://i.imgur.com/d5tgVKe.png)    

    At this point, if you try to run the game, it will behave as it did earlier, except for a thrown error, which says that there's no EventfulNetworkManager in the scene, that's expected, and will be fixed in the next step

### Enhancing The Network Manager

We now need to replace the standard NetworkManager with **EventfulNetworkManager**. It is pretty much the same as the regular NetworkManager, but instead of forcing you to override methods, it triggers events, to which other components can subscribe. MyGameServer component we've created earlier is one of those subscribers. 

1. Select `NetworkManager` object in the hierarchy, click **Add Component** button, and add `EventfulNetworkManager`. Now there will be two network managers.
2. Remove the `MiniNetworkManager` component by right clicking and selecting `Remove component`
3. Add the MiniPlayer and Coin prefabs to the list of spawnables (you can find prefabs at `Barebones/MiniGame/Resources/Prefabs`):

    ![](http://i.imgur.com/vPogsWG.png)

:warning: If you try to launch the game now, your _**player character will not be spawned**_. This is because in order to allow players to join, game server must first be registered to Master, and then opened to public. In the next steps, we'll use some of the "helper components" to automate this process for us.

## Joining The Game

General steps for client to join a game are as follow: 

1. Connect to Master
1. Log in
1. Retrieve access key
1. Send access key to game server

When you are developing your game, you probably want to test every iteration of changes as fast as possible. Normally you'd have to start the server, build a client, connect and login with the client and etc, just to get into the game. 

If you're using **uNET **for your games networking, there are a few scripts and prefabs that can automate this process for you, so navigate to `Barebones/MasterFramework/Prefabs`

1. Add **MasterServer** to scene. This script will automatically start master server when you hit "play" button in the editor
1. Add **GameServerStarter** prefab to scene- it will automatically start your game server (after master server starts)
1. Add **UnetConnector** prefab to scene - it has a flag, called **Auto Join If Host**, which, as the name suggests, will try to automatically join the game, by first connecting to master and logging in.
1. Add **Hud** prefab to the **Canvas** object in the hierarchy. It will help you see if the servers were started successfully.

After adding these prefabs, here's what your scene hierarchy should look like:

![](http://i.imgur.com/QV0bllF.png)

:white_check_mark: At this point, if you start the game in editor, Master server should start first, then game server, and after that - a user should join the game. This should be confirmed in your hud:

![](http://i.imgur.com/qJuBYfg.png)

:information_source: It's highly recommended that you take a look at how `GameServerStarter `and `UnetConnector `scripts work, especially if you're not using uNET, or if you're aiming for a custom flow of events. These scripts will give you a general idea of how you can setup your own development process 

## Persistent Coins

Even though we have added a coins property to the our custom profile, we never actually used it. To do that, we have to first retrieve the profile, read the value we want, and update it when user picks up a coin. 

To do that, open the `MyGameServer` script, and modify `OnServerUserJoined` method like this:

``` C#
/// <summary>
    /// Called, when authenticated user sends a valid access token
    /// </summary>
    /// <param name="client"></param>
    protected override void OnServerUserJoined(UnetClient client)
    {
        // Retrieve a profile first
        ProfilesModule.GetProfile(client.Username, MasterConnection, profile =>
        {
            if (profile == null)
            {
                // We failed to retrieve players profile, let's disconnect him
                DisconnectPlayer(client.Username);
                return;
            }

            // Get coins property
            var coinsProperty = profile.GetProperty<ObservableInt>(MyProfileKeys.Coins);

            // Spawn player (the same method we used earlier)
            var player = MiniNetworkManager.SpawnPlayer(client.Connection, client.Username, "knife");
            player.Coins = coinsProperty.Value;

            // Subscribe to the coins change listener.
            // It will be invoked on server, when client picks up a coin.
            player.OnCoinsChanged += () =>
            {
                // Update clients profile by setting a different coins value
                coinsProperty.Set(player.Coins);
            };
        });
    }
```

## Making Sure Everything Works

Build the game with two of your scenes, make sure that main scene is first.

![](http://i.imgur.com/yCQf7MC.png)

When you're in your game scene, hit "Play" in the editor. As before, it should start master and game servers automatically. You should be able to start the client you just built, and connect to the game.

⚠️ I accidentally published the version where game scene name is set to "AwesomeGame". Change it in Canvas > Create Game Window

![](http://i.imgur.com/Y46sjiy.png)