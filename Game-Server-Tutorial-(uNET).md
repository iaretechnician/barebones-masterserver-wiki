:fire: _Don't be discouraged by the length of this tutorial. All you'll actually need to do is add 3 scripts (that you don't even need to write) into the scene, and make a couple of small edits. If you know what you're doing, it's : 5 minutes tops_

## Overview

General steps to setting up a game server are as follows:

1. Add game server object (**IGameServer**) - it should handle receiving passes from clients, disconnecting players that are not allowed and etc.
1. Add server **starter script** - this should be a script that reads command line arguments and starts the game server accordingly (example: `BMGameServerStarter.cs`)
1. Add **connector script** - a script that connects client to game server, when client opens the scene. (example: `BMUnetConnector.cs`)
1. Setup **shutdown conditions** - you probably don't want users creating a bunch of empty games, so a script that handles some conditions, such as last player leaving the game, should always be present. (example: `BMServerTerminator.cs`)

In this tutorial, we'll use a basic uNET game, and try to properly connect it to Master Server.

## Mini uNET Game

To guide you through this process, I've written a very simple game which uses uNET. Game files will be located at `Assets > Barebones > MasterserverTutorial` folder. 

:information_source: If you're not familiar with uNET, I recommend you go through a tutorial in the [official unity's manual](https://docs.unity3d.com/Manual/UNetSetup.html)

## Try out the provided example

Open the **MasterTut** scene (_MasterTutComplete is a scene which is the end result of what we're doing_). The game is very simple and consists of 3 scripts and 3 prefabs. General steps to try the example:

1. Add scene to build settings as the first scene
1. Build the game
1. Host the game in unity editor
1. Join the game with the client you just built

If you've managed start the server (or host) in unity's editor and joined with client, congratulations! If you're having trouble, feel free to contact me. Here's what you should be aiming for:

![](http://i.imgur.com/D2hoH38.png)

## Implementing IGameServer interface

Our game server is only valid when it implements `IGameServer `interface. If you're using uNET HLAPI, general implementation is already written for you in script **`UnetGameServer.cs`**, this is what we'll be using to set things up faster. 

1. Create a new script in `MasterserverTutorial > Scripts`, and call it **TutGameServer.cs**

2. Make sure your newly created class extends `UnetGameServer`. To do that, you'll need to implement two methods: `OnServerUserJoined` and `OnServerUserLeft`. These methods are called when authenticated user joins and leaves a game.

3. Call `TutNetworkManager.SpawnPlayer(client.Connection, client.Username)` in the `OnServerUserJoined`. As you might have guessed, this will spawn player's character when player successfully joins the game. OnServerUserJoined is called when the player has officially joined the game. 

    Here's what your script should look like:

    ```C#
    using Barebones.MasterServer;

    public class TutGameServer : UnetGameServer {

        /// <summary>
        /// Called, when user successfully passes to game server
        /// </summary>
        protected override void OnServerUserJoined(UnetClient client)
        {
            TutNetworkManager.SpawnPlayer(client.Connection, client.Username);
        }

        /// <summary>
        /// Called, when user leaves a game server
        /// </summary>
        protected override void OnServerUserLeft(UnetClient client)
        {
        }
    }
    ```

4. Create a new empty game object as a child of **Networking **object and call it **GameServer**
5. Attach a newly created component to it.

    At this point, if you try to run the game, it will behave as it did earlier, except for a thrown error, which says that there's no EventfulNetworkManager in the scene, that's expected, and will be fixed in the next step

6. Edit **TutNetworkManager **to extend **EventfulNetworkManager**, instead of the regular NetworkManager. You will also be required to delete OnServerAddPlayer method, so go ahead and do it. Your file should only contain `SpawnPlayer `method 

    :information_source: EventfulNetworkManager script is pretty much the same as the regular NetworkManager, but instead of forcing you to override methods, it triggers events, to which other components can subscribe. TutGameServer component we've created earlier is one of those subscribers. 

:warning: If you try to launch the game now, your _**player character will not be spawned**_. This is because in order to allow players to join, game server must first be registered to Master, and then opened to public. In the next steps, we'll use some of the "helper components" to automate this process for us.

## Joining The Game

General steps for client to join a game are as follow: 

1. Connect to Master
1. Log in
1. Retrieve access key
1. Send access key to game server
1. Game servers spawns a player, 

If you're using **uNET **for your games networking, there are a few scripts that can automate this process for you. 

When you are developing your game, you probably want to test every iteration of changes as fast as possible. Normally you'd have to start the server, build a client, connect and login with the client and etc, just to get into the game.

We can start Master Server automatically, by adding the Master server prefab from `Barebones > MasterServer > Prefabs > MasterServer`. It's best to add it as a root object in the objects hierarchy, as it should not be destroyed when changing scenes.

Thankfully, unitys **uNET **has a built in Host mode which we can use to imitate a client and the server at the same time. There are two scripts that can help you automate the process: 

1. `GameServerStarter.cs` - this script handles command line arguments, which are provided when starting a game server process. If no arguments are provided, it will use default settings from the inspector.
1. `UnetConnector.cs` - this component handles connecting client to game server. If **AutoJoinIfHost **flag is set, connector will try to authenticate you and spawn a character into the server right after you start the server as a host - very helpful when developing your game.

Adding these two components is what we're going to do in the next steps. 

1. Add the MasterServer prefab to the scene (from `Barebones > MasterServer > Prefabs`)
1. Create a new empty game object as a child of **Networking **object and call it **Starter**
1. Attach to it component named **GameServerStarter**
1. Create a new empty game object as a child of Networking object and call it **Connector**
1. Attach to it component named **UnetConnector**
1. Your hierarchy and component settings should look somewhat like this:

![](http://imgur.com/Vi2FaJE)

:white_check_mark: At this point, if you start the game in editor, the server should be started automatically, and a character should be spawned for the account you have set in inspector to connect with (or guest account) 

If something doesn't work, check the settings of GameServerStarter and UnetConnector components in the inspector. Make sure the addresses are correct and Master Server is running.

:information_source: It's highly recommended that you take a look at how `GameServerStarter `and `UnetConnector `scripts work, especially if you're not using uNET, or if you're aiming for a custom flow of events. These scripts will give you a general idea of how you can setup your own development process 

## Making Sure Everything Works

Before publishing a game scene / game server, you want to make sure the following works:

* Started server appears in the listing
* Clients can join the game
* Users can create new game rooms with the scene you just created

To test this, we should build a client with these scenes in the settings: 
![](http://i.imgur.com/Vi2FaJE.png)

:warning: Make sure that _**Main scene is the first one**_, because it's the "lobby" of the game, through which clients will connect to our server.

_Start server _in the editor and _start the client_ from the build you made previously, log in as guest and you should see our server in the listing: 

![](http://i.imgur.com/wHZEOkO.png)

If you see the server in the listing, you should be able to join it

Next step should be to see if your users can create rooms. 

:warning: You should make sure that at least one **Spawner Server** is started and registered to **Master Server** 

In the **Main **scene, locate `Canvas -> BMCreateGame` object, and make sure it has your new level in map selection. 
![](http://i.imgur.com/ew1aNY6.png)