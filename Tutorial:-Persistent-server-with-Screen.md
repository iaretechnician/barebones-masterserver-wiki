This tutorial is aimed at those who want their game to stay up and running long term, as in, longer than you keep your SSH connection to your VPS open.

Talk to Trivn in the [MSF Discord server](https://discord.gg/ZqGummZ) if you have any issues!

This tutorial assumes:  
* You know how to open an SSH connection to your VPS and run commands to start your Server  
* You have uploaded your MasterServer (spawner optional) to your VPS  
(See [Building to a Linux VPS](https://github.com/alvyxaz/barebones-masterserver/wiki/Tutorial:-Building-to-Linux-VPS) for a walkthrough to these)  

---

## Step 1: Installing Screen

First, check if you have screen installed with:
`[root@yourserver~]# which screen`

If this returns a directory, you're in luck! If not, run the following commands to install screen:  
```
sudo apt-get update
sudo apt-get install screen
```

---

## Step 2: Starting Screen

Simply run `[root@yourserver~]# screen` to start screen. You may see a message covering the copy-write and some information about screen.

Press **Ctrl+a** to send commands to Screen instead of your shell. Nothing will show up when you press the keys, but the next key will be interpreted as a command towards Screen.

Example: Press `Ctrl+a ?`, that is, press Ctrl+a to start talking to Screen, then press "?" (shift+/) to send the ? command to Screen. This will list the key bindings for Screen. To use them, press the appropriate keys after Ctrl+a.

For a quick walkthrough on more about Screen, check out this [this blog post](https://www.rackaid.com/blog/linux-screen-tutorial-and-how-to/)

---

## Step 3: Starting the Master Server in Screen

First, start screen with `[root@yourserver~]# screen`.

Now navigate to the folder where your MasterServer executable is located and run the command to start the server.

At this point, everything should look just like normal. The fun part happens when you hit `Ctrl+a d` and disconnect from Screen.

Your MasterServer is now running in a Screen instance in the background! You can safely disconnect your SSH connection and the server will continue to run.

---

## Extra info:

Useful screen commands:  
(press keys after "Ctrl+a")  
* d -disconnects from the current screen instance  
* c -makes a new "tab" in the screen instance. You can switch between these with:  
* n -goes to the next "tab". It loops back to the first tab after you hit the end  
* k -kills the current screen "tab". You'll need to accept it with "y" afterward

---

## OPTIONAL STEPS

This is where we get a bit tricky. We're going to automate the Screen _and_ the MasterServer startup!

First, make a new document called "MasterServerStarter.sh" with the following text:

```
cd /directory/to/your/executable/
./MasterServer.x86_64 -msfStartMaster -msfStartSpawner -msfMasterIp xxx.xxx.xxx.xxx -batchmode -nographics -logfile
```

Set the directory, filename, arguments, and IP address appropriately for your setup. Upload the file to your server and put it in the same directory as your executable. Make sure to run `chmod 700 ./MasterServerStarter.sh` so you can execute it.

This file is a timesaver. You can run it instead of the lengthy command to start up your MasterServer. Running it through Screen will start it up the same as the above steps.

Next, make a second document called "MasterInScreen.sh" with the following text:

```

```