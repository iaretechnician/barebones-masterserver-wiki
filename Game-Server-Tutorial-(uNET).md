## Overview

General steps to setting up a game server are as follows:

1. Add game server object (**IGameServer**) - it should handle receiving passes from clients, disconnecting players that are not allowed and etc.
1. Add server **starter script** - this should be a script that reads command line arguments and starts the game server accordingly (example: `BMGameServerStarter.cs`)
1. Add **connector script** - a script that connects client to game server, when client opens the scene. (example: `BMUnetConnector.cs`)
1. Setup **shutdown conditions** - you probably don't want users creating a bunch of empty games, so a script that handles some conditions, such as last player leaving the game, should always be present. (example: `BMServerTerminator.cs`)
