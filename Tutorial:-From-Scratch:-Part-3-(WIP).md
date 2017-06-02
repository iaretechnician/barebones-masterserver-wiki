Continuing from [Tutorial: From Scratch: Part 2](https://github.com/alvyxaz/barebones-masterserver/wiki/Tutorial:-From-Scratch:-Part-2)
In Part 2, we set up some displays to indicate what was going on in the background. Now we're going to actually set up the Game Server and have something for the client to do.

We're going to be adding a bit of code in this tutorial. Some will be new, some will be taken from the included assets. We're not going to be walking through this line-by-line, but I'll add comments to explain what different parts are doing. I recommend reading through the original scripts as well to better understand what is going on, as well as what is possible to add.

----

# Step 1

Make a new scene called **scratchGame** and delete everything inside it. We don't need to see what's happening, as this is running on the server where no one will look.  
This isn't going to be the game itself but a scene that is spawned by the Spawner and will run the server side of the game scene.

Next, throw in a *ConnectionToMaster* prefab. Now it'll find the MasterServer, but it still doesn't have anything to show game-wise. So let's make a game.

# Step 2

Make another new scene called **scratchLevel**. This is going to be a simple game as a proof of concept, but after we're done here, feel free to make it as complex as you want.

First, set the *Main Camera* and *Directional Light* Transforms as shown:
![](http://i.imgur.com/p5lbxvR.png)
![](http://i.imgur.com/PsPsVwu.png)

Add *ConnectionToMaster* object.

Add a GameObject called *StartPosition* and add the component *NetworkStartPosition*. Change its position and rotation to "0,0,0". (This is part of Unity's networking, not MSF)

### Network Manager

Make a GameObject called *NetworkManager* with a new script called *ScratchNetworkManager.cs*.

This is just a trimmed down version of the *NetworkManagerSample* included in the BareBones assets.

`
`

The following scripts are fairly generic so they can be used for many different kinds of games. We will (probably) be covering how to make custom versions in their own tutorial later. (If there is a tutorial, please link it here or contact "Trivn" in the discord)

Make a GameObject called *UnetGameRoom* and add the included script *UnetGameRoom.cs*.

Make a GameObject called *RoomTerminator* and add the included script *UnetGameTerminator.cs*.

Make a GameObject called *UnetServerStarter* and add the included script *UnetServerStarter.cs*.

And that wraps up the level!