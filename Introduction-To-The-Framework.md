⚠️ I'm currently editing this document in real time, so "be careful" ^_^

## What is The Master Server Framework

In general, **Master Server** is a type of server to which everything connects (a central server). 

Master Server Framework is a framework designed to kick-start the development of such server.

#### A Few Points About The Framework

* ℹ️ **UI is decoupled from the functionality** - UI components are included in the asset mainly to demonstrate how you can use the API. For example, if you want to display Game Servers list in a special way, you don't have to use the example provided - you can make request to server, and display the data received however you want
* ⚠️ **Source code should not be edited** - asset will be updated regularly, and your changes might get lost in the process of updating. To avoid this, modify functionality by extending classes and listening to events.
* ℹ️ **You don't have to use everything** - framework can be useful for many games, but not many games will need all the functionality. Sometimes, all you might need is authorization and a custom module

## Architecture

To make Barebones Framework more scalable, backend logic was split into three main types of servers (server instances):
* **Master Server** - this is the central server, which handles authentication and connects together other types of servers
* **Spawner Server** - responsible for spawning game servers within it’s host. When client request to create a room, master server finds an appropriate spawner server and sends it instructions to create a game. More about this process (TODO)
* **Game Server** - this is where players connect to play the actual game.

![](http://i.imgur.com/aYM4jqx.png)

:information_source: You don't need different machines for your servers. If you want, you can host everything on a single 5 USD/month virtual server