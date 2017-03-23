### Accessing argument values

All of the arguments can be accessed through `Msf.Args`, for example:

``` C#
var port = Msf.Args.AssignedPort;
```

ℹ️ I forgot to comment them, but you'll find comments on what each of them means starting from V2.0.2

`Msf.Args.Names` contains properties with exact names of arguments, for example:

``` C#
var portArgName = Msf.Args.Names.AssignedPort; // "-msfAssignedPort"
```

### Checking if Argument is Provided

``` C#
if (Msf.Args.IsProvided(Msf.Args.Names.MasterIp))
{
    Logs.Error("Master IP argument was provided");
}
```

### Using Custom Arguments

There are some helpful methods to try and parse custom command line arguments. Let's say you've added a custom argument like this: `./Build -myMagicNumber 42`

To extract it, you can do this:

``` C#
if (Msf.Args.IsProvided("-myMagicNumber"))
{
    // Extract integer value
    var number = Msf.Args.ExtractValueInt("-myMagicNumber");

    // Extract string value
    var str = Msf.Args.ExtractValue("-myMagicNumber");
}
```

## List Of Used Argument names

| Argument       | Default value| Description|
| -------------  |:-------------:| -----:|
| **General** | |
| -msfMasterIp | null | Ip, which will be used when connecting to master server|
| -msfMasterPort | 5000 | Port, which will be used to connect to master server
| -msfMachineIp | null | Public IP of the machine, on which the process is running
| -msfLoadScene | null | Name of the scene, which should be loaded (you'll need a script which reads this argument, otherwise - nothing will happen)
| **Master** | |
| -msfStartMaster | false (not provided) | Starts master server|
| -msfMasterPort | 5000 | Master server will open this port|
| -msfDbConnectionString | null | Database connection string, which can be used by some of the database implementations
| **Spawner**  | |
| -msfExe | null| Path to executable, which should be used to spawn a process
| -msfMaxProcesses | 0 (infinite) | Max number of processes, that this spawner can spawn
| -msfSpawnBatchmode| false (not provided) | If provided, will spawn processes in batchmode. ⚠️ Removed in V2.0.2 - will spawn in batchmode automatically, if spawner is in batchmode
| **Spawned Process**  | |
| -msfAssignedPort | -1| Port, which is assigned by spawner to a spawned process
| -msfSpawnId | -1| Spawn id, which is used to identify which process is spawned for which request
| -msfSpawnCode | null | Code, which is used to check if there's no tampering with spawned processes
| -msfLobbyId | -1 | Id of the lobby, for which the process was spawned
| **Other** | |
|-msfWebgl | false (not provided) | Used to check if server should support WebGL clients