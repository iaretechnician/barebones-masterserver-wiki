![](http://i.imgur.com/IHbHOxT.png)

## Server 

### Sending E-mail Messages

A portion of the auth module functionality (resetting a password or confirming an e-mail), requires an ability to send e-mail messages. SMTP server is not within the scope of this framework, so you'll have to setup one of your own or use third-party services, like google's Gmail.

You can configure access to an smtp server by changing default settings in the inspector, or by providing these command line arguments when starting a master server:

| Argument       | Default value| Description|
| -------------  |:-------------:| -----:|
| -smtpUsername | taken from inspector | SMTP username (for example 'username@gmail.com') |
| -smtpPassword | taken from inspector | SMTP password |
| -smtpHost | taken from inspector | SMTP host (for example 'smtp.gmail.com')|
| -smtpPort | taken from inspector | SMTP port (for example '587') |

### Changing E-mail Sending Method

If you want to use a third-party library for sending mail, or if you find current implementation of sending mail not sufficient for your needs, you can override `AuthModule.SendMail` method.

## Client API

` Auth` is a static class that handles most of the authentication functionality. It contains a number methods that can help you with user authentication.

### Registering a user
`Auth.Register(Dictionary<string, string> data, AuthCallback doneCallback)`

Sends a registration request to master server, and invokes a callback when it's done. Data is provided as a string dictionary, which should contain at least these values: 

* **username **- username of the registering user
* **password **- password of the registering user
* **email **- email of the registering user

### Logging In

`Auth.LogIn(Dictionary<string, string> data, AuthCallback doneCallback)`

`Auth.LogIn(string username, string password, AuthCallback doneCallback = null)`

`Auth.LogIn(string token, AuthCallback doneCallback = null)`

`Auth.LogInAsGuest(AuthCallback doneCallback = null)`

Every method sends a login request (a dictionary of strings) to the master server. There are several overloaded methods that take specific arguments, such as username and password, and put them into dictionary for you. Master Server handles that dictionary to determine what's your account. 

If you send a request with dictionary of strings, these are the values that are handled: 

* **guest **- if this key is found in dictionary, it's assumed that we want to log in with guest account
* **token **- this is used to handle "remember me" functionality. If a token is found in the dictionary, server will try to find who was the last user with this token. If it's invalid or expired - authentication fails.
* **username **- username is checked together with password. If they match a specific user, authentication is successful
* **password**

### Loggin Out

`Auth.LogOut()`

Deletes a token (if there was one), disconnects from server and connects back. This approach is used to make sure that logged out user gets a new session, and there's no data "hanging" from the previous user. 

### Confirming an E-mail

`Auth.RequestEmailConfirmationCode()` - calling this method will send a request to master server, which will send an e-mail confirmation code to the e-mail address of a user who is logged in. 

`Auth.ConfirmEmail(string code, AuthCallback callback)` - sends a request to master server, which uses the code given to confirm clients e-mail

### Changing a Password

`Auth.RequestPasswordReset(string email, AuthCallback callback)` - sends a request to master server, which sends a password-reset code to the given e-mail.

`Auth.ChangePassword(PasswordChangeData data, AuthCallback callback)` - sends a request to master server, which users data provided to change a password.
