When setting up the framework, the first thing you should do is start Master Server, as it's the center point to which everything connects.

You can start the server by either starting an already compiled executable, or build and run the server from IDE.

## Running the server from IDE

1. Open the `MasterServer.sln` file with your IDE
1. Set `Barebones.MasterServer.Demo` as startup project
1. Right click on demo project -> Properties -> Debug, and add the command line arguments: `-master` . This will ensure that when you run the application, an argument to start master server is added.
1. Run the project through Debug -> Start Debugging (or Start Without Debugging)

You should start seeing some information on the screen. Probably something like this: 

![](http://i.imgur.com/iHpKJl2.png)

:white_check_mark: If there are no errors, master server should be running. 

## Running from pre-compiled exe

1. Start Command Prompt in **Complete **folder ([Windows tips](http://superuser.com/questions/946860/how-to-open-windows-cmd-so-that-it-starts-in-the-current-folder))
2. Run this command: 
    `Barebones.MasterServer.Demo.exe -master`

    This command starts master server with default parameters. More arguments are supported, and they can be found in section below. 

:white_check_mark: At this point, if there are no errors, the master server should be running

## Arguments
