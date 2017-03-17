### Accessing Account Data of The Client Who Is Logged In

These methods should most likely be used on the Client.

``` C#
// Check if client is logged in
if (Msf.Client.Auth.IsLoggedIn)
    Debug.Log("Client is logged in");

// Access info of user who is logged in
var accountInfo = Msf.Client.Auth.AccountInfo;
Debug.Log("Logged in as: " + accountInfo.Username);
```

### Accessing Data From a Remote Server

If a remote server, for example, a game server, needs to get account data of a user, remote server will need to make a request:

``` C#
// This is a peer id, which was given to client when he connected to master server
var peerId = 5; 
Msf.Server.Auth.GetPeerAccountInfo(peerId, (info, error) =>
{
    if (info == null)
        Debug.LogError(error);

    Debug.Log(info);
});
```

### Logging In

``` C# 
//--------------------------------------
// Log in with credentials
Msf.Client.Auth.LogIn("username", "password", (successful, error) =>
{
    Debug.Log("Is successful: " + successful + "; Error (if exists): " + error);
});

//--------------------------------------
// Guest login
Msf.Client.Auth.LogInAsGuest((successful, error) =>
{
    Debug.Log("Is successful: " + successful + "; Error (if exists): " + error);
});

//--------------------------------------
// Generic login (if you need more data)
var data = new Dictionary<string, string>
{
    {"username", "Username"},
    {"password", "Password123"}
};

Msf.Client.Auth.LogIn(data, (successful, error) =>
{
    Debug.Log("Is successful: " + successful + "; Error (if exists): " + error);
});
```

### Register a New Account

``` C#
//--------------------------------------
// Register a new account
var registrationData = new Dictionary<string, string>
    {
        {"username", "Super Username"},
        {"password", "Super Password"},
        {"email", "myEmail@email.com"}
    };

Msf.Client.Auth.Register(registrationData, (successful, error) =>
{
    Debug.Log("Is successful: " + successful + "; Error (if exists): " + error);
});
```