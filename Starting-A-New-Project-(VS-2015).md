This section will cover how you should setup your project for easy development. 

:star: Result of this tutorial should be included in the asset you acquired. If you're lazy, you can just copy the whole solution and work with it there :)

## Create a New Solution

1. In Visual studio, go `File > New > Project`
2. Choose `Templates > Visual C# > Console Application`, and name it YourGame

This should have created a new solution, with startup project called **YourGame**. This project is going to hold our server code, and will compile into a console application, which we will use to start the server.

## Create a Common Project

We need a common project, which will be compiled into a .dll and shared between server and client.

1. Right click on your solution, then `Add > New Project`
2. This time, choose `Templates > Visual C# > Class Library`. Make sure it's **Class Library**. Name it **YourGame.Common**
3. We need to change Target Framework to **.NET 3.5**, because Unity doesn't support anything higher. Right click on your project, go to `Properties > Application` and change the TargetFramework to 3.5. 
4. You can delete the `Class1.cs` file, which was created automatically

## Adding Source

We should start by adding Barebones MasterServer code into our solution. You can do that by referencing DLL files, but I think you'd be happier if you added the projects, so you could navigate through source code if necessary.

Right click on your solution, `Add existing project`, and navigate to `MasterServerSource` folder

Now you'll need to add these projects:
* `Barebones.MasterServer` - main master server project
* `Barebones.MasterServer.Common` - files, shared between master server and client 
* `Barebones.MasterStarter`_(optional) - includes code to help you get started (parses cmd args, initializes objects and etc.)
* `Barebones.Networking` - networking API code
* `Barebones.Networking.Server` - helpful server scripts

If you have imported all of the projects, here's what you should have:

![](http://i.imgur.com/2InoccI.png)

## Adding dependencies