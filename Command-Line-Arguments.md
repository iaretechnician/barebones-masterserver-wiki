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