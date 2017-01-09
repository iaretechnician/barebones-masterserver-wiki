# Setup

### 1. Create a New Scene Named "MasterServer" In The "MasterServer" project

### 2. Create a "wrapper" (parent) for our objects

* Create an empty game object
* Name it **Networking**

### 3. Create a Master Server object

* Create an empty game object as a child of **Networking**
* Name it **Master Server**
* Add **Master** Component 
* Select "Auto Start In Editor" in inspector

![](http://i.imgur.com/HmqjbI8.png)

**Auto Start In Editor** option will make sure that when you hit "Play" in the editor, master server will start automatically

> **Alternative:** Create a new script that will find **Master** in the scene, and call the `master.StartServer()` whenever you want (click of a button and so on)

### 4. Add The Hud To Scene

* Create a canvas in the scene: `GameObject > UI > Canvas`.
* Drag a prefab from `Barebones/MasterFramework/Prefabs/Ui/Hud` to the Canvas

### 5. Add LogController to the scene

Drag a prefab from `Barebones/MasterFramework/Prefabs/Ui/LogController` to the scene

> **What the hell is LogController?**. For logging, framework uses a small wrapper. It allows developers to implement custom log listeners (appenders). Great use case for it would be to have a custom appender which sends logs to another server (like a central log listener), so that you can see what's happening in your "cloud". At the moment of writing, logging is still a **work in progress**

Hierarchy view after all of these steps (Note: **EventSystem** appears automatically after creating canvas (I think)):

![](http://i.imgur.com/Us31621.png)

# Test If It Works

### Editor

Hit a play button in the editor, and if you've selected the "**Auto Start In Inspector**" option, it should start automatically, and Hud should indicate that the Master Server is running:

![](http://i.imgur.com/T9UM9nq.png)

### Build

Simply make a build, and run it with **-bmMaster** argument. You should see Hud with the same indication
