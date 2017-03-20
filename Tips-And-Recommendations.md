### Start From Basics, Don't Rewrite Demos

> I've noticed that many users like to start by rewriting the demo to suit their games. And when something doesn't work - they spend a large amount of time trying to understand what's wrong. 🐎 It's actually **a lot faster** to do everything from scratch, than trying to rewrite demos

I really recommend to start working with the framework "from scratch", instead of trying to hack the demo's to fit your game.

The thing is - in each demo, there's a lot going on. There are things that you need, there are things that you don't need, and other things that you want to change. It's very easy to get lost in all of this.

Instead, a better approach would be to start small, for example:
1. Get a basic Master Server working
1. Get a basic client to connect to master server
1. Do a basic authentication
1. Learn how to register a game server to master server
1. Learn to use the spawner
1. And so on...

Smaller steps will help you to make sure that you **understand how everything works**, and when something doesn't work - you'll know exactly what's wrong.

### Use the API instead of UI examples

All of the UI components that come with the framework were included **only as examples**. 

That doesn't mean you can't use them - you might be too lazy to create your own "games list" or "lobby", or you might just want to get a basic prototype working as soon as possible.

However, every game is different, and it's very difficult to write generic UI components that work for all games and all platforms. 

Take for example a "Games List". You might want to add a new column, remove some buttons, change the appearance. Then, later on, I decide to release an update, which changes the games list completely - all of your changes are overwritten, and the whole thing would probably stop working.

Instead, it's probably easier to call the API method:

`Msf.Client.Matchmaker.FindGames((games) => DisplayGames(games))`

And display the games however you want.

Or for authentication, all you probably need is this:

``` C#

// Log in
Msf.Client.Auth.LogIn(Username.text, Password.text, (accountInfo, error) =>
{
    // Failed to log in
    if (accountInfo == null)
        return;

    // Successfully logged in
});

// Register
var data = new Dictionary<string, string>
{
    {"username", Username.text},
    {"password", Password.text},
    {"email", Email.text}
};

Msf.Client.Auth.Register(data, (successful, error) =>
{
    // Failed
    if (!successful)
         return;

    // Success
});

```