This chapter will focus on explaining how game servers can be started by spawner, or manually, by the developer.

ℹ️ To keep this tutorial technology independent, we'll be using a "**Fake game server**" that we've created on the previous chapter of the tutorial

### Overview

In many cases, game servers are using the same build as the client. This is done mainly because game server logic usually intertwines with clients (Prefabs, RPC's, collision meshes and etc).

So, if it's the same build, how does Unity tell if we want to run a client or game server? It doesn't, so we need to do it manually - usually with command-line arguments.

There are two main ways to start a game sever:
* **Manually / Directly** - this would, usually mean starting the build / executable by hand
* **Via Spawner server** - by sending a request to an appropriate spawner server, which would take care of starting the game server. Usually, **Master** server server sends a request to spawner, however, master server can take requests from users and send a request to spawner seamlessly (as if the user requested it directly).