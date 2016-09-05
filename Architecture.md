To make Barebones Framework more scalable, backend logic was split into three main types of servers (server instances):
* **Master Server** - this is the central server, which handles authentication and connects together other types of servers
* **Spawner Server** - responsible for spawning game servers within it’s host. When client request to create a room, master server finds an appropriate spawner server and sends it instructions to create a game. More about this process (TODO)
* **Game Server** - this is where players connect to play the actual game.

![](http://i.imgur.com/aYM4jqx.png)

:information_source: You don't need different machines for your servers. If you want, you can host everything on a single 5 USD/month virtual server