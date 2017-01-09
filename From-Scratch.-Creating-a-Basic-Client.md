In this page, we will go through the steps of connecting a client to the master server

# Setup

### 1. Create a new scene and name it "Main"

### 2. Create a new empty object and name it "Connection"

![](http://i.imgur.com/4WherCc.png)

### 3. Add a component "MasterServerConnector"

This component will connect to your Master Server.

![](http://i.imgur.com/WWjMzBn.png)

> **Editor Address** - this address will be used when testing in the editor

> **Skip If Server** - when this property is set, it will check if we're running a master server or any other server from command line. If we do, it will **skip** the connection to master server.

### 4. :warning: Make sure the Master Server is running

Master server should be running (check the previous tutorial about starting it)

### 5. Add the Hud

Add it the same way you did in a previous tutorial

# Test if it works

Hit the Play button in the editor, and Hud should indicate that you're connected:

![](http://i.imgur.com/e8r7BdL.png)
