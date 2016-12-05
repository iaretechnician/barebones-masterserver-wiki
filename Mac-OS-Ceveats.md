## All Ports need to be >1024

All framework versions up to v1.03 have a default port for master server set to 90. This won't work on MacOS, and you'll need to change it. Here's how I get demo running on MacOS and a fresh installation of Unity:

* Open Unity
* Create a new Unity project
* Install the framework asset
* Open **DemoMain** scene
* Change port in hierarchy Barebones > Master Server >Client Port to **1500**
* Change port in hierarchy Barebones > Connection > Port to **1500**
* Hit the editor "Play" button, and everything should be working

## Path To Executable

### Running Spawner Server In Editor

When you run Spawner server on editor, make sure to change the path to executable

![](http://i.imgur.com/UfsGuuC.png)

(path in the example: `/Users/admin/TestProject/Build.app/Contents/MacOS/Build`)