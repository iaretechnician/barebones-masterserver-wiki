## What Is Master Server

In general, **Master Server** is a type of server to which everything connects (a central server). 

Master Server Framework is a framework designed to kick-start the development of such server.

**Master Server** itself is actually a pretty simple script, which:
* Listens to a specific port for clients, so that clients can connect to master server
* Looks for modules in the scene and initializes them.

## Adding The Master Server To Scene

You can add the Master Server to **any scene** by creating a game object and adding `Master` component to it. Here's an example of the most simple Master Server you can have:

![](http://i.imgur.com/0eQIJPf.png)

To add modules, you simply add new game objects and attach module components (scripts) to them. It doesn't matter where you place your modules in the scene, for example:

![](http://i.imgur.com/Cv05aoI.png)

⚠️ Some of the modules have dependencies (other modules they depend on). If you add such a module without adding it's dependency, its `.Initialize(IMaster master)` method **will not be called**.

## Starting The Master Server

There are three ways in which you can start the Master Server:

1. In the editor: `Master` component has a flag in the inspector called "**Auto Start In Editor**". When it's set, Master Server will be started automatically when you hit the Play button in the editor.
2. Through command line arguments: 🔗 [Command Line Arguments](https://github.com/alvyxaz/barebones-masterserver/wiki/Command-Line-Arguments#list-of-supported-arguments)
3. By manually calling `Master.Start()` in your scripts.

You'll probably want to start the server in editor when you're developing, through command line in production, and manually - if you have something special planned (a UI, for example).

ℹ️ `Master` component has a flag in the inspector called "**Auto Start In Editor**". When it's set, Master Server will be started automatically when you hit the Play button in the editor.

ℹ️ `Master` component also looks for a special command line argument

## Connecting to Master Server From Client

You can connect to master server simply by calling

`Connections.ClientToMaster.Connect(IpAddress, Port)`

Or you can do it manually by creating a socket 🔗 [More info on Networking](https://github.com/alvyxaz/barebones-masterserver/wiki/Networking-API#connecting-with-a-client)

## Modules Explained

Modules are simple unity scripts (MonoBehaviours) intended to **add functionality** to Master Server.

As described above, master server merely initializes other modules. You can find modules in the `Barebones/MasterModules/` folder.

![](http://i.imgur.com/JxvmvFQ.png)

Let's say you've decided to add a shop to your game. Shop processes (buy/sell, etc.) should be handled in the central server (master server).

So, to add new functionality (shop) to the master server, you can create a **ShopModule**, which will handle buy / sell requests from clients.

When working on a game, your Master Server will be a combination of custom modules and modules from the framework

:link: [**More info about creating modules**](https://github.com/alvyxaz/barebones-masterserver/wiki/Creating-Modules)

ℹ️ Its recommended to have modules as children of `Master` component, because then you can save it as a prefab and use in other scenes when developing. 

## Modifying/Extending Modules

Modules are unity components, and can be **extended/modified by overriding their virtual methods**