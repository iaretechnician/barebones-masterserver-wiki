## First, Caveat Emptor!

I am providing this as a courtesy to the community, because a few people have asked me for details on how I implemented it. Beware! This code may not work for you! It's a bit of a hack, and could probably be done in a cleaner way. I am not volunteering to support you in your attempts to implement this code!

If you have any doubts about your understanding and ability to fix any problems you may encounter, please DO NOT USE THIS CODE. Have a nice day. :)

But if you still want to continue...

## Default Behavior

The provided AuthModule (as of MSF version 2.0.3, April 2017) offers functionality to allow clients to login, and then, while logged in, request a six-byte alphanumeric confirmation code sent to their email address. Copying that confirmation code back into the game client can authenticate the user's email address.

## Desired Changes to Default Behavior

However, some developers may feel that it's more secure to require the user's email address to be confirmed before allowing them to login to your master server. Here is a quick mod that will force the master server to send the confirmation email immediately upon user registration. Consequently, all users are required to either have already confirmed their email address, or they need to submit the code along with their user name and password the first time they login.

## Server Modifications

To implement this modification on your server, edit **AuthModule.cs** (or create a subclass to do something similar if you prefer). Add the following code into the HandleLogIn function, in the Username/Password authentication section, after the accountData is retrieved, and the user's password has been validated.

	if (!accountData.IsEmailConfirmed)
	{
		// Email address not yet confirmed, so let's try to confirm it now...
		if (data.ContainsKey("confirmationCode"))
		{
			var confirmationCode = data["confirmationCode"];
			if (db.GetEmailConfirmationCode(accountData.Email) != confirmationCode)
			{
				message.Respond("Incorrect confirmation code".ToBytes(), ResponseStatus.UnconfirmedAccount);
				Logs.Warn("PASSWORD AUTHENTICATION failed. Incorrect confirmation code for account: - " + accountData.Username);
				return;
			}

			// now the email confirmation code is confirmed
			accountData.IsEmailConfirmed = true;
			db.UpdateAccount(accountData);

			// need to remove row from db email_confirmation_codes
			db.RemoveEmailConfirmationCode(accountData.Email);

			// Invoke the event
			if (EmailConfirmed != null)
			{
				EmailConfirmed.Invoke(accountData);
			}
		}
	}

Two other small changes are required. In **IAuthDatabase.cs**, add the following method signature (anywhere inside the interface declaration):

	void RemoveEmailConfirmationCode(string email);

And finally, in **ResponseStatus.cs**, add a new enum value after the NotHandled definition:

	UnconfirmedAccount = 9,

## Client Modifications

As with most things in any Client-Server system, the client needs to change as well, to match the changes in the server. Most of these changes are in **MsfAuthClient.cs**. First, we're going to replace the signature for LoginCallback with a new one, near the top of the class declaration. Add the new ResponseStatus parameter, as follows:

        public delegate void LoginCallback(AccountInfoPacket accountInfo, string error, ResponseStatus status);

Then, around the middle of the same file, add a couple more overloads for the LogIn method (without removing or changing any of the other existing LogIn methods), as follows:

	/// <summary>
	/// Sends a login request, using given credentials
	/// </summary>
	public void LogIn(string username, string password, string confirmationCode, LoginCallback callback, IClientSocket connection)
	{
		LogIn(new Dictionary<string, string>
		{
			{"username", username},
			{"password", password},
			{"confirmationCode", confirmationCode}
		}, callback, connection);
	}

	/// <summary>
	/// Sends a login request, using given credentials
	/// </summary>
	public void LogIn(string username, string password, string confirmationCode, LoginCallback callback)
	{
		LogIn(username, password, confirmationCode, callback, Connection);
	}

Most of these LogIn method definitions are simply custom ways to call the "real" LogIn method, which is the last one you'll find. It has a signature of:

    public void LogIn(Dictionary<string, string> data, LoginCallback callback, IClientSocket connection)

In that method definition, the client callback method is Invoked four different times, depending on what happens during processing. We're just going to add an appropriate ResponseStatus code as an extra parameter to each Invoke, because the server is going to send it to us. Here's how that method should look when you're done:

	/// <summary>
	/// Sends a generic login request
	/// </summary>
	public void LogIn(Dictionary<string, string> data, LoginCallback callback, IClientSocket connection)
	{
		if (!connection.IsConnected)
		{
			callback.Invoke(null, "Not connected to server", ResponseStatus.NotConnected);
			return;
		}

		_isLogginIn = true;

		// We first need to get an aes key 
		// so that we can encrypt our login data
		Msf.Security.GetAesKey(aesKey =>
		{
			if (aesKey == null)
			{
				_isLogginIn = false;
				callback.Invoke(null, "Failed to log in due to security issues", ResponseStatus.Failed);
				return;
			}

			var encryptedData = Msf.Security.EncryptAES(data.ToBytes(), aesKey);

			connection.SendMessage((short) MsfOpCodes.LogIn, encryptedData, (status, response) =>
			{
				_isLogginIn = false;

				if (status != ResponseStatus.Success)
				{
					callback.Invoke(null, response.AsString("Unknown error"), status);
					return;
				}

				IsLoggedIn = true;

				AccountInfo = response.Deserialize(new AccountInfoPacket());

				callback.Invoke(AccountInfo, null, status);

				if (LoggedIn != null)
					LoggedIn.Invoke();
			});
		}, connection);
	}

Note that after you make these changes, you'll need to modify any calls to LogIn, to ensure that they can receive the response callback including the ResponseStatus third parameter. For example, if you're using the demo/sample LoginUI, you might do something like change this:

    Msf.Client.Auth.LogIn(Username.text, Password.text, (accountInfo, error) =>

to this:

    Msf.Client.Auth.LogIn(Username.text, Password.text, (accountInfo, error, status) =>



