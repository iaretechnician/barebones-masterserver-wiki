:information_source: The purpose of this page is to help you determine whether or not you need this asset. If, after reading everything, you're still not sure if this asset is for you, feel free to send me a message with your questions or feature requests

## Before You Buy

This asset is **not a complete project**, but instead a framework, which you'd use to develop your back-end servers. Making multiplayer game is not a trivial task, so it's expected that you're familiar with writing C# code.

**Framework Doesn't Replace Gameplay Networking Solutions

Your game servers will need to run on **uNET **or something else. PM me or ask in forum thread for more info

## What Is The Master Server Framework

It's a framework that helps you write back-end servers for your games, with ease, **within unity**, without having to worry about third-party services. It contains boilerplate code for user authentication, dynamic / automatically synchronized player profiles, and highly scalable game servers (rooms) architecture. 

At the time of writing, included modules are:
* **Authentication module** - manages user registration / logging in
* **Profiles module** - build your player profiles without having to worry about database structures or synchronizing profiles between servers.
* **Game Servers module** - allows to register game servers to master server, so that they can be found by players (**lobby**), or used to create matchmakers. Game servers can access player profiles and update them (give exp on kill, etc.)
* **Spawner Servers module** - allows to spawn game servers on users request (user created game rooms). Spawned servers will have full authority - users don't need to act as hosts, which allows even **Browser and mobile users** to create game rooms - no need for relay servers or NAT punch-through.
* **Chat module** - simple chat module with support for channels and private messaging

Servers and modules communicate to each other through **Barebones Networking API**

## Networking API 

It's a layer of abstraction on top of networking technologies / protocols, that simplifies communication between servers and clients. 

It allows you to easily start any number of socket servers (think "[Photon Server](https://www.photonengine.com/en/OnPremise)", just **without monthly subscriptions, CCU limits and with Linux support**), which you can use to create any kind of server you want. 

It's decoupled from master server, so you're free to write any kind of socket-based server you need. For more info and code samples, [click here](https://github.com/alvyxaz/barebones-masterserver/wiki/Networking-API).

## Focus Points Of The Framework

When designing the framework, here's what I've been aiming for:
* Easy Prototyping - you should be able to start working on your game/server right away, without having to go through numerous configurations, projects and countless setup steps.
* High Extend-ability - if one component doesn't fit your needs, you should be able to modify, replace it or not use it at all
* No third-party services - dependencies, in general, suck. Paid dependencies suck even more.
* Cheap Maintenance - servers can run on cheap VPS options, or on your own machine, if you fancy it.
* Scalability - architecture must be scalable across multiple machines to support an increase of players
* Networking Abstraction - in case there's something wrong with an underlying networking code, you can simply switch technologies and libraries without having to change your client and server communication code.

## Why Run Every Server With Unity's Process

I used to think that Unity had a huge overhead. As it turns out - it doesn't! Launching a single headless unity instance on a 5$/month machine takes about **30 MB's of RAM and 3% of CPU!**

Here are some of the best features of using unity as a server:
* **Blazing Fast Development Cycle** - If you've ever worked in the environment, where your workflow was: edit server, build, start server, share dll's, edit client, start client, connect to server... You'd probably be amazed to hear that you can click a Start button in your editor, and everything starts automatically, in the editor, and your changes are visible instantly, for both client and server.
* **All code and resources are shared** - insanely great feature for prototyping. Let's say you have a game with a shop. When you create a new item in your game, your servers can pick it up and put into the shop automatically - no manual labor when trying to make sure items match in all servers and clients
* **Works offline** - no third party services are used, so you can run everything locally
* **No Concurrency** - I love concurrent programming, I thrive in it, but after working on a server that has no way of getting hit by race conditions, I don't think I'm going back. "Single-threaded" server-side code is a breeze to work with. "But what about my CPU cores?" - you might ask? Trust me, there are many ways to use them (like running multiple sever processes, or running intensive tasks in separate threads (yes, you can still use concurrency if you want, so yeah, you don't really lose a thing)).

**Rapid development** alone should attract you, because that's where most of your money and time can be saved. If these points didn't convince you, and you still think you shouldn't run your serves on Unity - I'd love to hear what you're working on and what worries you