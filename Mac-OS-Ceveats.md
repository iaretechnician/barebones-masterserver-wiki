## Start "Things" With Sudo

When you try to start a socket server on Mac OS, it will require you to start the process with elevated permissions. Here's how you start unity with elevated permission:

`sudo /Applications/Unity/Unity.app/Contents/MacOS/Unity`

## Path To Executable

### Running Spawner Server From Build

:warning: Normally, if you don't supply a parameter for a path to executable, spawner server uses the same executable that it was started from. However, version v1.01 fails to resolve a path to "current file", so you'll need to override it manually. 

You can do that by passing an absolute path to build with parameter `-bmExe`, for example:

`sudo /Users/admin/Documents/Build.app/Contents/MacOS/Build -bmMaster -bmSpawner -bmExe "/Users/admin/Documents/Build.app/Contents/MacOS/Build"`

**This will be fixed in an update v1.02**

### Running Spawner Server In Editor

When you run Spawner server on editor, make sure to change the path to executable

![](http://i.imgur.com/UfsGuuC.png)

(path in the example: `/Users/admin/TestProject/Build.app/Contents/MacOS/Build`)