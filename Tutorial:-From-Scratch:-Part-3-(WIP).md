Continuing from [Tutorial: From Scratch: Part 2](https://github.com/alvyxaz/barebones-masterserver/wiki/Tutorial:-From-Scratch:-Part-2)
In Part 2, we set up some displays to indicate what was going on in the background. Now we're going to actually set up the Game Server and have something for the client to do.

----

# Step 1

Make a new scene called **scratchGame** and delete everything inside it. We don't need to see what's happening, as this is running on the server where no one will look.  
This isn't going to be the game itself but a scene that is spawned by the Spawner and will run the server side of the game scene.

Next, throw in a *ConnectionToMaster* prefab. Now it'll find the MasterServer, but it still doesn't have anything to show game-wise. So let's make a game!

# Step 2

Make another new scene called **scratchLevel**. This is going to be a simple game as a proof of concept, but feel free to make it as complex as you want!

