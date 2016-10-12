![](http://i.imgur.com/IHbHOxT.png)

:information_source: _This wiki is constantly updated, so it's not recommended to use the documentation provided with the asset - it's most likely outdated_

## Contents Of The Wiki

Under the hood, framework is quite simple, but variety of different concepts might be a bit difficult to grasp at first - these guides are here to help you find your away around it.

🔥  _**If you found a section that's lacking**, or if you want something covered in the documentation, post a feature request here, in GitHub (Issues tab)_, or send me a personal message. I know how awesome it is to own a product with great documentation, so I'll be more than happy to assist. 🌞 

1. [**Introduction to the framework**](https://github.com/alvyxaz/barebones-masterserver/wiki/Introduction-To-The-Framework) - great read to understand "The Big Picture"
1. Demo Related
 1. [**Demo Explained**] (https://github.com/alvyxaz/barebones-masterserver/wiki/Demo-Explained)
 1. [**Launching The Demo**] (https://github.com/alvyxaz/barebones-masterserver/wiki/Launching-The-Demo) - good place to start if you like to learn from an example
1. Development / Getting Started
 1. [**Master Server And Modules**](https://github.com/alvyxaz/barebones-masterserver/wiki/Master-Server-And-Modules) - a short guide that will help you better understand what is the Master Server and how modules work
 1. [**Integrating to uNET game**](https://github.com/alvyxaz/barebones-masterserver/wiki/Getting-Started) - short guide on implementing master server functionality in a simple uNET game.
 1. [**Creating Modules**](https://github.com/alvyxaz/barebones-masterserver/wiki/Creating-Modules) - tutorial on how to extend master server functionality with your own modules
 1. [**Send, receive and handle messages**](https://github.com/alvyxaz/barebones-masterserver/wiki/Networking-API) - this guide contains examples that will help you get familiar with networking abstraction layer.
 1. [**Tips on how to structure your project/scenes**](https://github.com/alvyxaz/barebones-masterserver/wiki/Project-Structure-Tips)
 1. [**Database**](https://github.com/alvyxaz/barebones-masterserver/wiki/Database)
1. Modules
 1. [**Profiles Module**](https://github.com/alvyxaz/barebones-masterserver/wiki/Player-Profiles)
 1. [**Chat Module**](https://github.com/alvyxaz/barebones-masterserver/wiki/Chat-Module)
 1. [**Game Rooms Module**](https://github.com/alvyxaz/barebones-masterserver/wiki/Game-Servers) - general overview, describing how game servers register to master.
 1. [**Spawner Server**](https://github.com/alvyxaz/barebones-masterserver/wiki/Spawner-Server) - describes inner workings of spawner server and how you can use it to spawn game servers.
1. Production
 1. [**Hosting your game**](https://github.com/alvyxaz/barebones-masterserver/wiki/Hosting-Your-Game)

## Feature Requests, Bugs and Improvement Ideas

I want to make your experience of running Barebones Master Server as simple, easy and convenient as possible.

Even if you've downloaded this illegally, feel free to contact me with ideas for improvements or feature requests.

## Editing Source code

It's highly advised that you don't make any changes to source code. In most cases, you should be able to make changes by implementing interfaces, extending classes or subscribing to events. If you edit the source, it's possible that your project will break when you download an update, because your changes will be overwritten.

_With that said, it's still possible that you might stumble upon an issue where there's no work around. In that case, make the changes and contact me, I'll try to provide a better access to what you need in the next update._