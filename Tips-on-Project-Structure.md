One of the most difficult concepts of the framework to grasp is understanding how everything starts, and how everything connects together.

The short answer is - you can do everything the way you want and the way it is convenient to you. There's really no preferred way to structure your code / scenes. If you find a specific setup convenient - it's a perfect setup.

I couldn't think of a better way to fill this page than with series of questions and answers.

## Should I Have Different Scenes For Different Servers?

Not necessarily. This approach might actually slow down your development process, because of scene switching.

ℹ️ When in production, I usually start servers in the main scene,. When working on the gameplay, I start them right in the scene of the game.

## Can I Have Servers In Separate Unity Projects?

Yes, you can, but you will need to figure out a way to share code between them. You can also develop everything in one project, then split things when you're ready to go into production.

ℹ️ It's recommended to have everything in one project, because it's more efficient to work this way.

## How Can One Build (project) Act As Server And a Client?

One build can contain code for servers and the client, but everything won't work at the same time. It's up to you to decide how you want to start the servers and the client. 

You could create a special scene with buttons "Start Master Server", "Start Spawner Server" and etc., and make a special build for administrator, but it's not very convenient. And most of the time you'll want your server to run in **batchmode** so no UI will be visible. 

When starting a server in **batchmode** (through command line / terminal / console and etc.), you can set a special argument, for example `-startMaster`. You could then setup your main scene to check if this argument is given, and start the server automatically if it is.

Example in the Demo is set up this way.

## Can I Do The Whole Project in One Scene?

Yes, you can! This approach is great for development, or for small games.

## Tip For Faster Development

Even if you have a special scene in which your clients connect to Master Server, authorize and do other stuff, it's great to automate this process in your game scenes, so you don't have to jump through scenes every time you want to test the game.

An example might illustrate this point better:

It wouldn't be very efficient, if every time you made a change to your game, you had to make a build, start the Master Server, start Spawner server, then start a client, connect to master server, login with your credentials, create a game, and then join the game. It sounds more like a nightmare, rather than development process!

Instead, a better approach would be to automate the whole process by:
1. Adding a script to start the master server
2. When master server is started, establish a client connection with it.
3. Send an authentication request (login)
4. Start the game server as a host,
5. Register the game to master server
6. Send a request to join the game.

Framework actually contains the code that does this for you, there's more info in the 🔗 [Getting Started Guide](https://github.com/alvyxaz/barebones-masterserver/wiki/Getting-Started).

This way, when you hit a "Play" button in the editor, you go into the game instantly, without having to go through the "preparation" steps.
