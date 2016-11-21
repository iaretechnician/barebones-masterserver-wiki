This page is designed to give you a general idea on what this framework does, and what it's trying to solve

## What is a Server

![](http://i.imgur.com/nrphozU.png)

Server is a computer or a program that manages access to a centralized resource or a service. 

To put it plainly, in our context, server is a piece of software, which runs on a remote computer, to which clients can connect.

ℹ️ Framework contains "Networking API" which allows you to easily create "empty" socket servers, in Unity, **from scratch**. Core of the Master Server is created using this API.

### Why Would Anyone Need a Server For Their Game?

Having a server allows you to do all kinds of things - chats, authentication, "cloud saves", authoritative shops and etc. Even for simple things, such as "daily gifts", you'd need a server to make sure that a player doesn't hack the client to retrieve an infinite amount of free stuff.

You could use a server to build a forum in your game, or a suggestions box. (these features are not in the framework, but you can use it to make them)

## What is The Master Server (In General)

![](http://i.imgur.com/PW2NHrv.png)

In general, **Master Server** is a type of server to which everything connects. Not just clients (players), but game servers and other types of servers too.

This means that master server **knows about everyone who connected to it**, and can use this information to do various things:

* Tell players which game servers are running
* Assign players to game servers
* Allow players to communicate to each other when they play in different servers

## What is The Game Server

![] (http://i.imgur.com/azBQXyN.png)

Game server is a server which is resposible for **gameplay logic**.

Usually, game servers have these characteristics:

* Large network traffic consumption - all the flying bullets, all the moving objects and characters need to be constantly synchronized between players, and it consumes a lot of traffic
* Higher CPU usage - quite often, it needs to run physics, calculate collisions, do raycasting, process inputs from all of the players and etc.

## What is The Master Server **Framework**

Master Server Framework is a framework designed to kick-start the development of **Master Server**. 

It contains solutions to some of the common problems when developing master servers, and servers in general.

Networking API included in the framework allows you to build not just a master server, but **any kind of server you want**, within unity.

## The Big Picture

![](http://i.imgur.com/72zLq6U.png)

To make Barebones Framework more scalable, backend logic was split into three main types of servers (server instances):
* **Master Server** - the central point to which everything connects
* **Game Server** - this is where players connect to play the actual game.
* **Spawner Server** - responsible for spawning game servers within it’s host. When client requests to create a room, master server finds an appropriate spawner server and sends it instructions to create a game. 

All of these servers can run within a single VPS or on your local machine. However, when your player base increases, you will need to support more game servers. To do that, you'll have need to have more VPS'es or dedicated servers running.

On one of my games, game server consumes about 70 MB ram, and about 5% of CPU. On a 2GB RAM VPS (10$/month), I can run about 20 game servers, with 30 players each, which allows me to have 600 concurrent users for just 10$/month, which is very, very cheap. 

With current architecture of the framework, numbers can be doubled by buying another VPS. 

## A Few Points About The Framework

* ℹ️ **UI is decoupled from the functionality** - UI components are included in the asset mainly to demonstrate how you can use the API. For example, if you want to display Game Servers list in a special way, you don't have to use the example provided - you can make a request to server and display the data received however you want
* ⚠️ **Source code should not be edited** - asset will be updated regularly, and your changes might get lost in the process of updating. To avoid this, modify functionality by extending classes and listening to events.
* ℹ️ **You don't have to use everything** - framework can be useful for many games, but not many games will need all the functionality. Sometimes, all you might need is authorization and a custom module