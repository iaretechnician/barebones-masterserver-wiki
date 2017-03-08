:information_source: _This wiki is constantly updated, so it's not recommended to use the documentation provided with the asset - it's most likely outdated_

--

## NEWS

**2017 03 08** | Still working hard on the V2.0. Got a bit swamped with e-mails, but it's nearing the completion! I apologize to everyone, to whom I've promised to release earlier. It's a bit more work than expected, and I'm trying to make everything as perfect as I can :). If you have suggestions or questions - email me, or catch me on Discord!

**2017 02 28** | I've opened a Discord server for those who want to chat: [https://discord.gg/ZqGummZ](https://discord.gg/ZqGummZ)

⚠️🔥 **2017 02 28** | Some of the **V2.0** functionality is uploaded to the private github repository, for those of you who are interested - feel free to play around with it and send your feedback. Documentation is still not ready, and demo doesn't cover everything, so it's only recommended for more advanced developers. If you don't have an invitation to private GitHub repository - send me an e-mail with your invoice ;)

---

## ⚠️ V2.0 announcement

I'm rewriting the framework to make it API focused, cleaner, easier to use and extend (this is a VERY short description of a very big change)

***What about development until the V2 is released?*** If it's a new project - I'd recommend to wait until the V2, because it will take some time to learn V1.03, and by that time, there should be V2 preview available. 

If you are already using the V1.03 on existing project - you should probably keep using it, and switch to V2 when you have some free development time :)

-----

## Contents Of The Wiki

Under the hood, framework is quite simple, but variety of different concepts might be a bit difficult to grasp at first - these guides are here to help you find your away around it.

_**If you found a section that's lacking**, or if you want something covered in the documentation, post a feature request here, in GitHub (Issues tab)_, or send me a personal message. I know how awesome it is to own a product with great documentation, so I'll be more than happy to assist. 🌞 

1. [**Introduction to the framework**](https://github.com/alvyxaz/barebones-masterserver/wiki/Introduction-To-The-Framework) - great read to understand "The Big Picture"
1. ⚠️ 🔴 [**Important Stuff**](https://github.com/alvyxaz/barebones-masterserver/wiki/Important-Stuff)
1. [**Updates (v1.03.1)**](https://github.com/alvyxaz/barebones-masterserver/wiki/Updates) :new:
1. :new: :fire: **From Scratch** -Great place to understand the framework
 1. [**Introduction**](https://github.com/alvyxaz/barebones-masterserver/wiki/From-Scratch.-Introduction)
 1. [**Setting Up Unity Projects**](https://github.com/alvyxaz/barebones-masterserver/wiki/From-Scratch.-Setting-Up-The-Projects)
 1. [**Basic Master Server**](https://github.com/alvyxaz/barebones-masterserver/wiki/From-Scratch.-Creating-a-basic-Master-Server)
 1. [**Basic Client**](https://github.com/alvyxaz/barebones-masterserver/wiki/From-Scratch.-Creating-a-Basic-Client)
 1. [**Adding Authentication module**](https://github.com/alvyxaz/barebones-masterserver/wiki/From-Scratch.-Adding-Authentication)
 1. [**Adding Games Module**](https://github.com/alvyxaz/barebones-masterserver/wiki/From-Scratch.-Adding-Games-Module)
 1. [**Registering Game Server :new:**](https://github.com/alvyxaz/barebones-masterserver/wiki/From-Scratch.-Registering-a-Game-Server) - guide on how to register any kind of game server (even a fake one) to the master server
 1. **Automating game server launching** ( ⚠️ I'm working on the V1.04 update, which will slightly change the way the framework sets up. It's going to take about a week to finish. (Date of writing: 2017.02.02). I'll finish this tutorial when the update is completed)
 1. **Using what we know to setup an existing uNet game server** (WIP)
1. Demo Related
 1. [**Demo Explained**] (https://github.com/alvyxaz/barebones-masterserver/wiki/Demo-Explained)
 1. [**Launching The Demo**] (https://github.com/alvyxaz/barebones-masterserver/wiki/Launching-The-Demo) - good place to start if you like to learn from an example
1. Development / Getting Started
 1. [**Master Server And Modules**](https://github.com/alvyxaz/barebones-masterserver/wiki/Master-Server-And-Modules) - a short guide that will help you better understand what is the Master Server and how modules work
 1. [**Integrating to uNET game**](https://github.com/alvyxaz/barebones-masterserver/wiki/Getting-Started) - short guide on implementing master server functionality in a simple uNET game.
 1. [**Creating Modules**](https://github.com/alvyxaz/barebones-masterserver/wiki/Creating-Modules) - tutorial on how to extend master server functionality with your own modules
 1. [**Networking API. Send, receive and handle messages**](https://github.com/alvyxaz/barebones-masterserver/wiki/Networking-API) - this guide contains examples that will help you get familiar with networking abstraction layer.
 1. [**Tips on how to structure your project/scenes**](https://github.com/alvyxaz/barebones-masterserver/wiki/Project-Structure-Tips)
 1. [**Database**](https://github.com/alvyxaz/barebones-masterserver/wiki/Database) :new:
 1. [**Command Line Arguments**](https://github.com/alvyxaz/barebones-masterserver/wiki/Command-Line-Arguments) - a list of default arguments you can use when starting servers
1. Modules
 1. [**Authentication**] (https://github.com/alvyxaz/barebones-masterserver/wiki/Authentication)
 1. [**Profiles Module**] (https://github.com/alvyxaz/barebones-masterserver/wiki/Player-Profiles)
 1. [**Chat Module**](https://github.com/alvyxaz/barebones-masterserver/wiki/Chat-Module)
 1. [**Game Rooms Module**](https://github.com/alvyxaz/barebones-masterserver/wiki/Game-Servers) - general overview, describing how game servers register to master.
 1. [**Lobbies Module**](https://github.com/alvyxaz/barebones-masterserver/wiki/Lobbies-Module) :new:
 1. [**Spawner Server**](https://github.com/alvyxaz/barebones-masterserver/wiki/Spawner-Server) - describes inner workings of spawner server and how you can use it to spawn game servers.
1. Platform Specific
 1. [**Mac OS Ceveats**](https://github.com/alvyxaz/barebones-masterserver/wiki/Mac-OS-Ceveats)
1. Production
 1. [**Hosting your game**](https://github.com/alvyxaz/barebones-masterserver/wiki/Hosting-Your-Game)

## Editing Source code

It's highly advised that you don't make any changes to source code. In most cases, you should be able to make changes by implementing interfaces, extending classes or subscribing to events. If you edit the source, it's possible that your project will break when you download an update, because your changes will be overwritten.

_With that said, it's still possible that you might stumble upon an issue where there's no work around. In that case, make the changes and contact me, I'll try to provide a better access to what you need in the next update._