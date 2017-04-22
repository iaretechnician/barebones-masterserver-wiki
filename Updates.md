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