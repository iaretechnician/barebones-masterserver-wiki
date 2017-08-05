[**Release Notes**](https://github.com/alvyxaz/barebones-masterserver/blob/master/MasterServerFramework2/Assets/Barebones/MSF%20ReleaseNotes.txt)

### Version: V2.0.4 (Micro update)
* Fixed "Disconnect" event when client is running on editor
* Added protected methods to trigger events in AuthModule
* Elevated `AuthModule.IsLoggedIn` to protected
* Fixed passwords in spawned rooms

### V2.0.3 (Micro update)
* Multiple Chat module fixes
* Updated MySQL implementation
* Added PostgreSQL implementation (thanks to Arcanor!)

### V2.0.2 (Micro update)

* **Added "Quick Setup" demonstration**
* Spawned servers will now copy "-batchmode" argument from spawner
* Fixed "nographics" bug (missing space after nographics arg)
* Force disconnect when reconnecting
* QuickAuth will reapper after logging out
* Added "-msfDontSpawnInBatchmode" for when you don't want game servers
  to copy "batchmode" argument from spawners
* Fixes "lobby master" update
* Added helper method to retrieve public IP 
* Exposed logged in users (authModule.LoggedInUsers)
* Renamed "GetCompletionData" to "GetFinalizationData"

### V2.0.1 (Micro update)

* Added connection test from Emil Raneiro
* Added the '-nographics' tag to spawned game servers in batchmode
* Changed the name of the argument, which forces game servers to be spawned in batchmode : "**-msfSpawnBatchmode**"
* Renamed some of the events, to remove the "On" prefx, for example: "**OnConnected**" now becomes "**Connected**"