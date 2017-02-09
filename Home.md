:information_source: _This wiki is constantly updated, so it's not recommended to use the documentation provided with the asset - it's most likely outdated_

## ⚠️ V2.0 announcement

As you might have heard, I was recently working on v1.04 (which was supposed to make the framework easier to use, for both beginners and advanced developers). While working on it, I've tried to minimize the amount of breaking changes, but realized that to make meaningful changes, a large amount of breaking changes is **unavoidable**.

Today, **I know much more about how you use the framework** than I did when I first started working on it. Now, I can use this knowledge to make something greater.

### How long will it take, and how much will it cost?

My estimate is about 2 weeks - probably until the **27th of February**. And it's a **free update**.

I'll try to release testable versions earlier, so that those of you who are interested in the process can give me feedback, or just see what to expect from v2 early on.

### What will the new framework look like

* Main focus on simple to use **API**
* **Multithreaded** database accesses - accessing db is currently the most resource-intensive work
* Single port for all connections 
* Core structure will remain the same - there will be **master** and **spawner** servers, and master server will remain to be modular, so most of your custom scripts and modules should still work, or work with minimal changes
* Underlaying **Networking API will remain the same**
* Scripts to ensure that servers, after crashing, can be started again automatically
* Reduced interference with your game code

### What if I have a project in production with current version?

There are two options:

* Upgrade from v1.03 to v2 - most likely, it will not be too difficult to upgrade, unless you've got a huge amount of custom extensions
* Keep using the v1.03 - it's stable, and if you don't want to upgrade - don't.

### What about production until the V2 is released?

You can keep working on your project with V1.03, and upgrade later on. Don't think that it's a waste of time working with something that will be replaced soon - upgrading should not be too difficult, and most of your custom functionality will remain the same

ℹ️ If you have suggestions, feel free to share them in forums, by sending a PM, e-mail, or by starting an "issue" in github (the best option)

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