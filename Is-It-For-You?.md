:information_source: The purpose of this page is to help you determine whether or not you need this asset. If, after reading everything, you're still not sure if this asset is for you, feel free to send me a message with your questions or feature requests

[![](http://i.imgur.com/RB1F4Ym.png)](https://github.com/alvyxaz/barebones-masterserver/wiki/Introduction-To-The-Framework)

## To Those Who Are Too Lazy to Read Everything

* Framework focuses on helping you to build a Master Server, not Game Server (you'll use uNET or something else for that)
* Servers can run locally, but for production, you'll probably need to host them on a VPS or a dedicated server
* Framework encourages having game servers within your host (fully authoritative), and not on your client computers. You can either have some pre-defined game servers running 24/7, or you can have a Spawner server "Spawn" a game server on clients request (this imitates client hosting).
* Framework is all about the programming API. UI elements are just there to show you how you can use the framework on your games. For example - if you want to display a list of ongoing games, you don't need to use the UI that comes with the package - you can make a request manually, and display received data however you want.

## :warning: **Read this** :warning: 

**After the first month in the asset store, here are a few things that I feel needs to be said regarding the direction and vision of the framework**

To any inexperienced developers **who are not willing to learn** things or get their hands dirty - **you should not buy this**. It's not a magic solution - it's a convenient tool for **developers**.

 This framework is a lot like web frameworks, except it runs in Unity and instead of HTTP protocol, uses long lasting and stateful TCP / UDP connections (high performance, low overhead, just like a game server). When you start using a web framework - you don't expect it to develop the whole website for you automatically, instead, you're happy you don't need to do authorization and url routing. The same goes for this framework - it's a framework, not a complete game. It contains some helpful modules out of the box, and you can either use them, or use the core of the framework to develop your own.

When you buy it, you don't buy a black box which you should be afraid to touch and extend. **It's made to be extended, it's a framework!**

It seems like this framework attracts people mainly because of the **Spawner server** module, which, to them, seems like a holy grail to avoid uNET services. You need to understand that it's just a helpful module, and not exactly the main feature of the framework. **The main feature is that you can develop the Master Server within Unity**, which I personally find incredibly convenient.

If you're bad at programming and if you have no experience in networking, you should not expect to be able to buy this framework, throw it into a game as it is, and expect everything to be ready for production in a few minutes. I'm sure some people can do this, but they usually have tons of experience. If you're a beginner, you'll need to spend time learning a lot of new concepts.

To conclude, **I'm not saying that beginners should not buy this** - it contains years of my knowledge and experience in developing back-end servers for multiplayer games, and if you're willing to learn - **you'll learn a lot**! And when you understand it, you'll be able to bake a game after a game in a matter of days!

If you're a beginner who wants to learn - feel free to contact me, and I'll do my best to teach and guide you. I love helping people. But if you're not willing to do any development on your own - I'd rather you ask for a refund.

## Before You Buy

This asset is **not a complete project**, but instead a framework, which you'd use to develop your back-end servers. Making multiplayer game is not a trivial task, so it's expected that you're familiar with writing C# code.

**Framework Doesn't Replace Gameplay Networking Solutions**

Your game servers will need to run on **uNET** or something else (Forge, Bolt, etc...). If you're not sure about something PM me, ask in forum thread or post a question here, in github's issues tab. [**More info**]. Framework works **with uNET out of the box**, but for anything else you'll need to write your own implementations. (https://github.com/alvyxaz/barebones-masterserver/wiki/Game-Servers#gameplay-networking)

### If I Use uNET, Does It Mean I Have To Use Unity Services?

No. Framework was designed to allow developers to use uNET without having to use costly Unity Services. :warning: **It's important to understand how the framework works around the limited CCU**

### How Does The Framework Get Around Unity's CCU limit?

Unity limits CCU for their matchmaker and their relay server. Framework includes other solutions for matchmaking, but there's no direct replacement for Relay servers. Relay server is used to allow users to host games when they are behind a router / don't have a public IP. Relay servers increases networking latency by a lot.

⚠️ **To avoid latency issues and CCU constraints, instead of allowing clients to host games directly on their machine, framework encourages to "spawn" game servers in your "cloud", which can be accessed by any client directly.**

This **does not** mean that you are not allowed to let clients host their games. You can do that, but framework does not include any NAT traversal solutions at the moment, so the issue where clients wont be able to connect to a host without a "public IP" will remain.

## What Is The Master Server Framework

It's a framework that helps you write back-end servers for your games, with ease, **within unity**, without having to worry about third-party services. It contains boilerplate code for user authentication, dynamic / automatically synchronized player profiles, and highly scalable game servers (rooms) architecture. 

At the time of writing, included modules are:
* **Authentication module** - manages user registration / logging in
* **Profiles module** - build your player profiles without having to worry about database structures or synchronizing profiles between servers.
* **Game Servers module** - allows to register game servers to master server, so that they can be found by players (**lobby**), or used to create matchmakers. Game servers can access player profiles and update them (give exp on kill, etc.)
* **Spawner Servers module** - allows to spawn game servers on users request (user created game rooms). Spawned servers will have full authority - users don't need to act as hosts, which allows even **Browser and mobile users** to create game rooms - no need for relay servers or NAT punch-through.
* **Chat module** - simple chat module with support for channels and private messaging

Servers and modules communicate to each other via **Barebones Networking API**

## Networking API 

It's a layer of abstraction on top of networking technologies / protocols, that simplifies communication between servers and clients. 

It allows you to easily start any number of socket servers (think "[Photon Server](https://www.photonengine.com/en/OnPremise)", just **without monthly subscriptions, CCU limits and with Linux support**), which you can use to create any kind of server you want. 

It's decoupled from master server, so you're free to write any kind of socket-based server you need. For more info and code samples, [click here](https://github.com/alvyxaz/barebones-masterserver/wiki/Networking-API).

## Why Run Servers Within Unity's Process

I used to think that Unity had a huge overhead. As it turns out - it doesn't! Launching a single headless unity instance on a 5$/month machine takes about **30 MB's of RAM and 3% of CPU!**

Here are some of the best features of using unity as a server:
* **Blazing Fast Development Cycle** - If you've ever worked in the environment, where your workflow was: edit server, build, start server, share dll's, edit client, start client, connect to server... You'd probably be amazed to hear that you can click a Start button in your editor, and everything starts automatically, in the editor, and your changes are visible instantly, for both client and server.
* **All code and resources are shared** - insanely great feature for prototyping. Let's say you have a game with a shop. When you create a new item in your game, your servers can pick it up and put into the shop automatically - no manual labor when trying to make sure items match in all servers and clients
* **Works offline** - no third party services are used, so you can run everything locally
* **No Concurrency** - I love concurrent programming, I thrive in it, but after working on a server that has no way of getting hit by race conditions, I don't think I'm going back. "Single-threaded" server-side code is a breeze to work with. "But what about my CPU cores?" - you might ask? Trust me, there are many ways to use them (like running multiple sever processes, or running intensive tasks in separate threads (yes, you can still use concurrency if you want, so yeah, you don't really lose a thing)).

**Rapid development** alone should attract you, because that's where most of your money and time can be saved. If these points didn't convince you, and you still think you shouldn't run your serves on Unity - I'd love to hear what you're working on and what worries you

## Focus Points Of The Framework

When designing the framework, here's what I've been aiming for:
* Easy Prototyping - you should be able to start working on your game/server right away, without having to go through numerous configurations, projects and countless setup steps.
* High Extend-ability - if one component doesn't fit your needs, you should be able to modify, replace it or not use it at all
* No third-party services - dependencies, in general, suck. Paid dependencies suck even more.
* Cheap Maintenance - servers can run on cheap VPS options, or on your own machine, if you fancy it.
* Scalability - architecture must be scalable across multiple machines to support an increase of players
* Networking Abstraction - in case there's something wrong with an underlying networking code, you can simply switch technologies and libraries without having to change your client and server communication code.