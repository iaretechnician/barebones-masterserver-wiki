## Client API

` BMAuth` is a static class that handles most of the authentication functionality. 

`BMAuth` uses default settings, which can be accessed through `BMAuth.Config`. You can override these settings by either changing them in your code, or by adding the BMAuth script into your scene and changing the properties through inspector _(before accessing config class, BMAuth checks if there's a script in the scene first)_. 

### Registering
`BMAuth.Register(Dictionary<string, string> data, AuthCallback doneCallback)`

Sends a registration request to master server, and invokes a callback when it's done. Data is provided as a string dictionary, by default, it should contains 

Dictionary should contain at least these values: 

* **username **- username of the registering user
* **password **- password of the registering user
* **email **- email of the registering user

### Loggin In

`BMAuth.LogIn(Dictionary<string, string> data, AuthCallback doneCallback)`

`BMAuth.LogIn(string username, string password, AuthCallback doneCallback = null)`

`BMAuth.LogIn(string token, AuthCallback doneCallback = null)`

`BMAuth.LogInAsGuest(AuthCallback doneCallback = null)`

Every method sends a login request (a dictionary of strings) to the master server. There are several overloaded methods that take specific arguments, such as username and password, and put them into dictionary for you. Master Server handles that dictionary to determine what's your account. 

If you send a request with dictionary of strings, these are the values that are handled: 

* **guest **- if this key is found in dictionary, it's assumed that we want to log in with guest account
* **token **- this is used to handle "remember me" functionality. If a token is found in the dictionary, server will try to find who was the last user with this token. If it's invalid or expired - authentication fails.
* **username **- username is checked together with password. If they match a specific user, authentication is successful
* **password**

### Loggin Out

`BMAuth.LogOut()`

Deletes a token (if there was one), disconnects from server and connects back. This approach is used to make sure that logged out user gets a new session, and there's no data "hanging" from the previous user. 