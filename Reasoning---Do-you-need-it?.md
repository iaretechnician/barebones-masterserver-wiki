This page contains ramblings that might help you decide whether or not you should buy/use the Master Server Framework. It covers some of the reasons for making the Framework.

## Focus Points Of The Framework?
* Cheap Maintenance - it's no secret, that self-hosting ()

## Why Write Every Server On Unity?

I used to think that Unity had a huge overhead. As it turns out - it doesn't! Hosting a single unity instance on a 5$/month machine takes about **60 MB's of RAM and 1% of CPU!**

Here are some of the best features of using unity as a server:
* **Blazing Fast Development Cycle** - If you've ever worked in the environment, where your workflow was: edit server, build, start server, share dll's, edit client, start client, connect to server... You'd probably be amazed to hear that you can click a Start button in your editor, and everything starts automatically, in the editor, and your changes are visible instantly, for both client and server.
* **All code and resources are shared** - insanely great feature for prototyping. Let's say you have a game with a shop. When you create a new item in your game, your servers can pick it up and put into the shop automatically - no manual labor when trying to make sure items match in all servers and clients
* **Works offline** - no third party server software is used, so you can run everything locally
* **No Concurrency** - I love concurrent programming, I thrive in it, but after working on a server that has no way of getting hit by race conditions, I don't think I'm going back. "Single-threaded" server-side code is a breeze to work with. "But what about my CPU cores?" - you might ask? Trust me, there are many ways to use them (like running multiple sever processes, or running intensive tasks in separate threads (yes, you can still use concurrency, if you want, so yeah, you really don't lose a thing)).

If these didn't convince you - I'd love to hear what you're working on and what your needs are. 

**Rapid development** alone should attract you, because that's where most of your money and time is saved.

## Story behind
Before releasing this framework, I actually rewrote it 3 times!

1. I was working on an MMO, and I didn't want to use Unity as a server (I thought the overhead would be huge). I didn't want to use **Photon server** as a backend, because they cost per month, add CCU caps and only run on Windows (what??). I ended up writing a basic MMO server, which has nothing to do with Unity, but as I approached path-finding related issues, I realized that I was stupid to dismiss using unity as a server.
2. I've started working on 