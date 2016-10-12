⚠️ I'm currently editing this document in real time, so "be careful" ^_^

## What is The Master Server Framework

In general, **Master Server** is a type of server to which everything connects (a central server). 

Master Server Framework is a framework designed to kick-start the development of such server.

#### A Few Points About The Framework

* ℹ️ **UI is decoupled from the functionality** - UI components are included in the asset mainly to demonstrate how you can use the API. For example, if you want to display Game Servers list in a special way, you don't have to use the example provided - you can make request to server, and display the data received however you want
* ⚠️ **Source code should not be edited** - asset will be updated regularly, and your changes might get lost in the process of updating. To avoid this, modify functionality by extending classes and listening to events.
* ℹ️ **You don't have to use everything** - framework can be useful for many games, but not many games will need all the functionality. Sometimes, all you might need is authorization and a custom module
## Workflow Example

## Workflow

### 1. Making a Game Prototype

Lets say we have made a single-scene FPS prototype. The game goes like this: 

1. We open the game, and we're instantly in the game scene
2. We click the "Host" button, our character spawns and starts playing the game.

We suddenly decide that this game is not yet ready for production, and it would be way better if there was authentication (**AuthModule**) and games list (**GamesModule**)), so you purchase the Master Server Framework.

### 2. Setting Up The Master Server

🔗 [More info on Master Server](https://github.com/alvyxaz/barebones-masterserver/wiki/Master-Server-And-Modules)

We have to make a decision - do we want everything be in one scene, or should we separate everything into "Game" and "Main Menu" scenes. 

Let's say we want to have two scenes, so these steps should be made:

1. Create a new scene
2. Add `Master` component to scene _(more info at the link above)_
3. Add required modules to scene: `AuthModule` and `GamesModule`

Now, if we start the scene in the editor, it should start the Master Server.

## FAQ

#### Do I Need To Start Everything In One Build?

#### When To Consider Including Framework Into Your Game

You can start using the framework at the same time you start developing your game, or you can integrate it into an existing game.