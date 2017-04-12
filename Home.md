### 
:warning: This wiki is for V2.x of the framework. If you're using V1.xx -  [**V1.03 documentation**](https://github.com/alvyxaz/barebones-master-v1/wiki)

### Going Free And Open Source

**I've pushed all the source code to this repository, and marked the asset to be Free** (which will take a couple of days to be approved). I guess not everyone's going to be happy about it, but it's the only way to move forward.

For the past few months, I've been stuck. Replying all the e-mails and chat messages took me more than 5 hours a day (you have to load other projects by other people, look into what they are doing and etc.), after which time I'd be too exhausted to work on the framework, and half a day wouldn't pass before replies start coming in. It's an endless loop of exhaustion.

One could say - "you don't need to support everyone", but reality is that you do, indeed, have to support everyone and reply to every e-mail. Otherwise you'd get crappy reviews that would kill the project and make your income non-existent. Who wants to sink all day into exhaustion for free?

This framework became into something I can't handle alone, which is why I'm making it free and open source. What does it mean for the future of the project?

* **More people collaborating to improve it** (making videos, tutorials, custom modules and bug fixes)
* I no longer need to slave for reviews, which means I can be less active in support, and **work on the actual framework**.

Oh, and the last thing - **I'm going off-the-grid for a month** - there's a bunch of stuff that needs to be done before the wedding, and I want to work on my game a bit - I can't push the framework forward if I have no game which actually uses it. **I'll still poke around** to see if there are high priority issues that need to be fixed, but I won't engage much in the conversations - I need to take a break from all the writing. This post alone took me 90 minutes to write, god dammit.

P.S. I hope you don't start "1-star" rating this asset, as it would harm all of the developers who are using it. Framework is no longer mine - **it's ours**. 

## News

* **2017 04 06** | Added [**Database implementations**](https://github.com/alvyxaz/barebones-masterserver/wiki/Database-Implementations) page
* **2017 03 30** | I sent V2.0.2 to asset store two times now, and they keep updating it to V2.0.1! I'm now pushing the new version as a whole (without intermediary .unitypackage)
* **2017 03 27** | I'm visiting my parents for a week, so I'll be a bit inactive during the time. I'll still reply, but a bit sparingly. Thanks for understanding!
* **2017 03 25** | Added [Quick Setup](https://github.com/alvyxaz/barebones-masterserver/wiki/Quick-Setup) page
* **2017 03 25** | V2.0.2 sent to asset store, should be approved within a few days
* [**History**](https://github.com/alvyxaz/barebones-masterserver/wiki/News-History)

## Table Of Contents (V2)

ℹ️ [**Main Changes In V2**](https://github.com/alvyxaz/barebones-masterserver/wiki/Main-Changes-in-V2)

⚠️ If you've downloaded framework from the asset store, you'll find V2.0 as a separate .unitypackage within `Barebones` folder. Read the `V2 README !IMPORTANT.txt` for more information.

_**If you found a section that's lacking**, or if you want something covered in the documentation - post a feature request here, in GitHub (Issues tab)_, or send me a personal message. 🌞 

1. [**Introduction to the framework**](https://github.com/alvyxaz/barebones-masterserver/wiki/Introduction-To-The-Framework) - a general introduction to the main concepts of the framework
1. [**Updates**](https://github.com/alvyxaz/barebones-masterserver/wiki/Updates) - (Latest: **V2.0.2**)
1. [**Folder Structure**](https://github.com/alvyxaz/barebones-masterserver/wiki/Folder-Structure)
1. API
   1. [**Rooms module examples**](https://github.com/alvyxaz/barebones-masterserver/wiki/API-examples.-Rooms)
   1. [**Spawners module examples**](https://github.com/alvyxaz/barebones-masterserver/wiki/API-examples.-Spawner)
   1. [**Chat module examples**](https://github.com/alvyxaz/barebones-masterserver/wiki/API-examples.-Chat)
1. Getting Started
   1. [**Quick Setup**](https://github.com/alvyxaz/barebones-masterserver/wiki/Quick-Setup)
   1. [**Core Of The Framework**](https://github.com/alvyxaz/barebones-masterserver/wiki/Getting-Started) - it's important to read and understand this before delving deeper into the framework
   1. [**Tips and Recommendations**](https://github.com/alvyxaz/barebones-masterserver/wiki/Tips-And-Recommendations) - some of my recommendations and tips for those, who want to work with the framework
   1. [**Rooms Demo**](https://github.com/alvyxaz/barebones-masterserver/wiki/Rooms-Demo)
   1. Worlds Demo
1. Development
   1. [**Spawning Game Servers**](https://github.com/alvyxaz/barebones-masterserver/wiki/Spawning-Game-Servers) - covers the whole process of spawning game servers (**doesn't matter** which technology you use for game networking)
   1. [**Custom modules**](https://github.com/alvyxaz/barebones-masterserver/wiki/Custom-modules) - basics of extending the MSF functionality
   1. [**Peer Extensions / States**](https://github.com/alvyxaz/barebones-masterserver/wiki/Peer-Extensions-and-Properties)
   1. uNET + Rooms Module
   1. [**Switching database implementations**](https://github.com/alvyxaz/barebones-masterserver/wiki/Database-Implementations)
   1. [**Command Line Arguments**](https://github.com/alvyxaz/barebones-masterserver/wiki/Command-Line-Arguments)
   1. [**Networking API / Sending and handling messages**](https://github.com/alvyxaz/barebones-masterserver/wiki/Networking-API) - serializing, sending and receiving messages between servers and clients
1. Production
   1. Debugging Connection
   1. Make sure your IP addresses are correct
   1. [**Performance considerations**](https://github.com/alvyxaz/barebones-masterserver/wiki/Performance-Considerations)
1. Modules
   1. [**Auth Module**](https://github.com/alvyxaz/barebones-masterserver/wiki/Auth-Module) - logging in / registering an account
   1. Chat Module
   1. Rooms Module
   1. [**Profiles Module**](https://github.com/alvyxaz/barebones-masterserver/wiki/Profiles-Module) - how to use the profiles module
   1. Spawners Module

## Editing Source code

It's highly advised that you don't make any changes to source code. In most cases, you should be able to make changes by implementing interfaces, extending classes or subscribing to events. If you edit the source, it's possible that your project will break when you download an update, because your changes will be overwritten.

_With that said, it's still possible that you might stumble upon an issue where there's no work around. In that case, make the changes and contact me, I'll try to provide a better access to what you need in the next update._