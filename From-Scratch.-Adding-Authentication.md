## Server Side

### 1. Add Auth module

* Create an empty game object as a child of **Master Server**
* Name it **AuthModule**
* Attach "AuthModule" component

> **Does it have to be a child of "Master Server"?** Nope, not really. It's just for convenience. Master server looks for modules in the whole scene

### 2. Add Database Factory 

* Create a new empty object as a child of "Master Server"
* Name it **Database**
* Attach **LiteDbFactory** component

![](http://i.imgur.com/2qKKAbT.png)

#### What's the database factory?

Instead of using databases directly, all of the modules use (depend on) simple interfaces. It means that modules don't really care about which database is used, as long as it implements the required interface. This allows us to switch database implementations when necessary.

Database factory is a central script, responsible for creating / giving concrete database implementations. In this example, we're using LiteDB.

## Client

Let's add a "Guest login" to our game. To do that

* Create a new empty game object to the Canvas
* Name it **LoginControls**
* Create a new script called **LoginControls**

``` C#
using Barebones.MasterServer;
using UnityEngine;
using UnityEngine.UI;

public class LoginControls : MonoBehaviour
{
    public Button GuestLoginButton;
    public Text Username;

    void Awake()
    {
        Username.text = "Not logged in";

        GuestLoginButton.onClick.AddListener(() =>
        {
            Auth.LogInAsGuest((successful, error) =>
            {
                // We've failed to log in
                if (!successful || error != null)
                {
                    Username.text = "Error: " + error;
                    return;
                }

                // Disable login button
                GuestLoginButton.gameObject.SetActive(false);

                Username.text = "Logged in as: " + Auth.PlayerData.Username;
            });
        });
    }
}
```

* Attach it to the LoginControls object
* Create a button as a child of LoginControls, and set it as **Guest Login Button** in LoginControls inspector
* Create a Text element as a child of LoginControls, and set it as **Username** in LoginControls inspector

![](http://i.imgur.com/CO7hxEx.png)

> **What about real login forms?** You can drag in form prefabs located in `Barebones/MasterModules/Authentication/Prefabs` and play around with those

I'll probably add more details on how to setup forms later. It's quite easy, and for now I think you guys can figure it out. If not - send me a message :).

## Testing If It Works

🍏 Make sure Master Server is running

* Hit "Play" in the editor of Client project
* Click on the button to log in

And you should see that you're authenticated as guest:

![](http://i.imgur.com/TyJlIVq.png)

