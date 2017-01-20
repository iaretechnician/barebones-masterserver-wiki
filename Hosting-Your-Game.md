### Players can't connect to game server, "Connecting to 127.0.0.1:xxxx"

If you've hosted your servers somewhere, and when players try to access a game server and it says "connecting to **127.0.0.1**", the most likely problem is that players try to connect to game server, which is located at "the same device" (127.0.0.1 address). 

This happens because game server doesn't know it's own public IP, thus, when giving access to player, sends it the default IP address (127.0.0.1). If game server is started by spawner, spawner should provide it's IP to game server through cmd args. 

In most cases, what happens is that spawner server doesn't know it's public IP, thus it cannot provide a valid IP to game servers. If you're starting a spawner server through command line arguments, you should add an argument "-bmIp xx:xx:xx:xx:xxxx" - it represents "the address of machine on which this executable is running". 

##

⚠️  Step by step tutorial is still in development

Your game and servers will be a simple executable file, which you will start on your host server (VPS or a dedicated machine). 

If you made a build **for Windows**, you'll probably start the server by running your build from Command Prompt, or by simply opening it (it will depend on your setup).

If you made a build **for Linux**:
There's a guide that goes through the steps of starting Unity process on Linux VPS. You can find a guide [here](https://noobtuts.com/unity/unet-server-hosting) (though, have in mind that there are better VPS providers than Namecheap)
