## Create a Spawner

### Minimal Example

``` C#
var defaultOptions = new SpawnerOptions();

// Registers a spawner to master server, so that master server knows about it's existance.
// Your spawner will receive requests to spawn processes (or something else,
// if necessary)
Msf.Server.Spawners.RegisterSpawner(defaultOptions, (spawner, error) =>
{
    if (spawner == null)
        Logs.Error(error);
});
```

### Customized Example

``` C#
var spawnerOptions = new SpawnerOptions()
{
    MaxProcesses = 0, // Unlimited,
    MachineIp = "127.0.0.1", // IP address of this machine, will be passed to game servers too
    Region = "US", // Region identifier, can be anything
    Properties = new Dictionary<string, string>()
    {
        // If you need spawner to have some extra properties
        {"ExtraProperty", "Whatever" }
    }
};

// Example of a more customized approach
Msf.Server.Spawners.RegisterSpawner(spawnerOptions, (spawner, error) =>
{
    if (spawner == null)
    {
        Logs.Error(error);
        return;
    }
            
    // Set the build path (default ''(empty string))
    spawner.Settings.ExecutablePath = "C:/Win/Build.exe";

    // Change whether or not the spawner process should run in batchmode
    spawner.Settings.RunInBatchmode = false;

    // (Optional) If you want to handle spawn requests manually
    spawner.SetSpawnRequestHandler((packet, message) =>
    {
        // We've got a request to spawn a new process
        // packet - contains spawn info
        // message - the original message of the request. You'll need to respond to it
        // with ResponseStatus.Success, if process started successfully
        var hasError = false;
        if (hasError)
        {
            // Example on how to handle errors
            message.Respond("A mysterious error", ResponseStatus.Failed);
            return;
        }

        // TODO Start a process or a virtual game server

        // Respond with success
        message.Respond(ResponseStatus.Success);
    });

    // (Optional) If you want to handle kill requests manually
    spawner.SetKillRequestHandler(spawnId =>
    {
        // This is a request to kill a spawned process
        // TODO Find a process by spawnId and kill it
    });
});
```
