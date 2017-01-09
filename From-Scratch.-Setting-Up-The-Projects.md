## 1. Create Two Unity Projects

Let's start by creating two Unity projects:

1. **MasterServer** - this project will be used to build our **Master server** with **Spawner**
1. **Client** - this is what our clients will use to play the game and connect to master server. However, it will not be just a client. Same build will be used for:
 1. **Game Server** 

#### ❓  Why Game Server And Client Use The Same Build?

Usually, when you work on your game with, for example, Unet, client and game server needs to match slightly (RPC's, Message structures, code and etc.). If you don't understand why, try creating a simple Unet game on a new project. Most of the time, client and game server are the same thing, except the server might run slightly differently due to, for example, conditional statements (if's).

> **Is this the only way?** Nope. You can separate them if you want, but it will be a bit less convenient to work that way, and you'll still need to share some of the code.

> **Are there cases when it would be a good idea to separate them?** Yes. Your game servers will be running on your machines, which usually have limited **RAM**. Your client might require 1 GB of RAM for things like textures, meshes, animations and sounds. Game servers often don't need those, and could use a "stripped" build without them, consuming, for example, only 100 MB RAM

## 2. Import The Master Server Framework To Each Of Them

> **Should I delete master server code from client, and vise versa?** Nope. You'd only cause yourself a headache without reeking any benefits. There's no measurable overhead, and your security (as I think this is why you'd even consider it) is at no risk for containing code for a simple 3rd party asset (your **own** master server code will be in another project)

## 3. Create a Folder With Name "FromScratch" (both projects)

This is what it should look like:

![](http://i.imgur.com/N5EZfve.png)
