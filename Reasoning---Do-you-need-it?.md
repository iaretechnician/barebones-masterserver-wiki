## What Is The Master Server Framework

It's a framework that helps you write backend servers for your games, with ease, without having to worry about third-party services. It contains boilerplate code for user authentication, dynamic / automatically synchronized player profiles, and highly scalable architecture. 

## What Can You Do with It 

#### Perfect for room-based games: (think standard shooter game with item shops, matchmakers and etc)
This asset is perfect for you. Master-Spawner-Game architecture will allow you to have rooms functionality out of the box. 
You won't need to worry about saving player meta stats (such as experience, kills, etc.) and synchronizing it between servers - it can be done automatically, with **Player Profiles module**.

#### If All You Need is 

Networking API allows you to easily start any number of socket servers (think "Photon Server", just **without monthly subscriptions, CCU limits and with Linux support**), which you can use to create create 

## Before You Buy

**Framework Doesn't Do "Game Server Networking"**

There are too many solutions you might already be familiar with (**Unity's uNET**, Photon, Forge, Bolt, etc. ).

:fire: This asset officialy supports **uNET** only (because it's free and officialy supported by Unity developers), but it doesn't mean you can't use anything else. 

If you're using something else, you will need to write your own **Connector** and `IGameServer` implementation, which is quite easy. Here's more info on  the subject: TODO

## Focus Points Of The Framework

When designing the framework, here's what I've been aiming for:
* Fast to prototype with -
* Highly Extendable - if one component doesn't fit your needs, it shouldn't stop you from using others.
* No third-party services to pay for
* Cheap Maintenance - servers can run on cheap VPS options
* Scalability - architecture must be scalable to support an increase of players
* Modularity -
* Networking Abstraction - 

## Why Run Every Server With Unity

I used to think that Unity had a huge overhead. As it turns out - it doesn't! Hosting a single unity instance on a 5$/month machine takes about **60 MB's of RAM and 1% of CPU!**

Here are some of the best features of using unity as a server:
* **Blazing Fast Development Cycle** - If you've ever worked in the environment, where your workflow was: edit server, build, start server, share dll's, edit client, start client, connect to server... You'd probably be amazed to hear that you can click a Start button in your editor, and everything starts automatically, in the editor, and your changes are visible instantly, for both client and server.
* **All code and resources are shared** - insanely great feature for prototyping. Let's say you have a game with a shop. When you create a new item in your game, your servers can pick it up and put into the shop automatically - no manual labor when trying to make sure items match in all servers and clients
* **Works offline** - no third party services are used, so you can run everything locally
* **No Concurrency** - I love concurrent programming, I thrive in it, but after working on a server that has no way of getting hit by race conditions, I don't think I'm going back. "Single-threaded" server-side code is a breeze to work with. "But what about my CPU cores?" - you might ask? Trust me, there are many ways to use them (like running multiple sever processes, or running intensive tasks in separate threads (yes, you can still use concurrency if you want, so yeah, you don't really lose a thing)).

**Rapid development** alone should attract you, because that's where most of your money and time is saved. If these didn't convince you - I'd love to hear what you're working on and what your needs are. 

## Story behind
Before releasing this framework, I actually rewrote it 3 times!

1. I was working on an MMO, and I didn't want to use Unity as a server (I thought the overhead would be huge). I didn't want to use **Photon server** as a backend, because they cost per month, add CCU caps and only run on Windows (what??). I ended up writing a basic MMO server, which has nothing to do with Unity, but as I approached path-finding related issues, I realized that I was stupid to dismiss using unity as a server.
2. I've started working on 