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

Our game server is only valid when it implements `IGameServer `interface. If you're using uNET HLAPI, general implementation is already written for you in script **`BMUnetGameServer.cs`**, this is what we'll be using to set things up faster. 