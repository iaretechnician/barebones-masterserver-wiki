Continuing from  [Part 3](https://github.com/alvyxaz/barebones-masterserver/wiki/Tutorial:-From-Scratch:-Part-3)  

This is going to be the last part of the "From Scratch" series of tutorials. In this final part, we're going to make a list of open game instances for a player to join. Once we have that, you will be able to have players join currently running games or make a new instance if they choose. 

Beyond that, what comes next is up to you! You can change the *scratchLevel* scene and playerPrefab to make your own game, adjust the style and structure of the *scratchClient* scene to your liking, or continue learning from other tutorials in order to add more features to your game.

Anyway, let's continue:

---

# Step 1

What we're going to be doing is re-making the "games list" from the QuickSetup tutorial, but leave more room for modification. For now, let's get the layout of the elements and add the scripts after.

First, open the **scratchClient** scene. Hide the button we were using to start the new game scene.

On the canvas, create a new *panel* element and name it **ServerList**. Set its *Transform* to fill the entire screen. This is going to hold all of our new elements for the sake of organization.

Create a new *panel* element and name it **Rooms** and two new *button* elements name **CreateGame** and **Refresh**. Position your elements and change their colours however you like. Mine looks like this (stole the colours from the demo):

![](http://i.imgur.com/OvjvH55.png)

Inside the **Rooms** element, add a *Scroll View* named **List** and a *scrollbar*. Set the scrollbar to *Bottom to Top* and line it up on the right side of the **Rooms** panel. Move the **List** Scroll view to the top of the **Rooms** panel and stretch it to the right until it reaches the scrollbar. 

Inside the **List** *Scroll Rect* component, set the Content to **List**, the Viewport to **Rooms**, and the Vertical Scrollbar to the scrollbar we just made. Add a *Content Size Fitter* Component and set *Horizontal Fit* to Unconstrained and *Vertical Fit* to Preferred Size.  

Add a new *Panel* named **Header** to **List**. Add a *Horizontal Layout Group* Component to **Header** and set it up like so:

![](http://i.imgur.com/vM5Wsok.png)

Add three *Text* boxes to the **Header** and name them **Join**, **Map**, and **Players**. Give all three a *Layout Element*component and set **Join** and **Players** to *Flexible Width* 0, and the **Map** to *Flexible Width* 40. It may take some messing around to get these right. (Picture will follow below)

Add a new Panel to **Header** and name it **Item**. This will essentially be a "Prefab" for our list item, but we're not going to make a prefab out of it. 

In the **Item** element, add a *Button*, and two *Text element*s named **Map** and **PlayerCount**. Add *Horizontal Layout Group* Components to each of these and set them the same as above (it may take some messing around again).

Finally, it should look like this (or something similar):

![](http://i.imgur.com/9UGeDt1.png)

----

# Step 2

Now that the layout is complete, let's get to the code.

Create the script *ScratchGameListItem* and add it to the **Item** panel.

### ScratchGameListItem

```
using UnityEngine;
using UnityEngine.UI;

namespace Barebones.MasterServer
{
    public class ScratchGameListItem : MonoBehaviour
    {
        //Button to click to join the game
        public Button joinButton;

        //Storing the packet that contains the information about the game instance
        public GameInfoPacket RawData { get; protected set; }
        //The list we're adding the element to
        public ScratchGamesList ListView;

        public Text MapName;
        public Text PlayerCount;

        //Placeholder in case something goes wrong
        public string UnknownMapName = "Unknown";

        //store the gameId for easy access when joining the game instance
        public int GameId { get; private set; }

        //Here we set up the item to display the information
        public void Setup(GameInfoPacket data)
        {
            //We take the raw data and extract the gameId
            RawData = data;
            GameId = data.Id;

            //the display for the player count
            if (data.MaxPlayers > 0)
            {
                PlayerCount.text = string.Format("{0}/{1}", data.OnlinePlayers, data.MaxPlayers);
            }
            else
            {
                PlayerCount.text = data.OnlinePlayers.ToString();
            }
            //the display for the map name
            MapName.text = data.Properties.ContainsKey(MsfDictKeys.MapName)
                ? data.Properties[MsfDictKeys.MapName] : UnknownMapName;
        }
        //This gets called when the client clicks on Join
        public void OnJoinClick()
        {
            Msf.Client.Rooms.GetAccess(RawData.Id, OnPassReceived);
        }

        //If the GetAccess call returns properly, everything will be handled automatically, otherwise, we display the error
        protected virtual void OnPassReceived(RoomAccessPacket packet, string errorMessage)
        {
            if (packet == null)
            {
                Msf.Events.Fire(Msf.EventNames.ShowDialogBox, DialogBoxData.CreateError(errorMessage));
                Logs.Error(errorMessage);
                return;
            }
        }
    }
}
```

Set the variables as follows:

*Join Button* = the button under the **Item** panel  
*Map name* = the **Map** text element in the **Item** panel  
*Player Count* = the **PlayerCount** text element in the **Item** panel  

We'll make the script for *List View* next.

Create the script *ScratchGamesList* and add it to the **Rooms** panel.

### ScratchGamesList

```
using System.Collections.Generic;
using System.ComponentModel;
using Barebones.Networking;
using Barebones.Utils;
using UnityEngine;
using UnityEngine.UI;

namespace Barebones.MasterServer
{
    public class ScratchGamesList : MonoBehaviour
    {
        //List of the game packets to make the list from
        private GenericUIList<GameInfoPacket> _items;
        //The list "prefab" we created
        public ScratchGameListItem ItemPrefab;
        //the layout group that holds the list items
        public LayoutGroup LayoutGroup;
        //the connection to the master server
        protected IClientSocket Connection = Msf.Connection;

        //when the client is started up, we want it to update automatically so the player doesn't need to click on "refresh" right away 
        protected virtual void Awake()
        {
            _items = new GenericUIList<GameInfoPacket>(ItemPrefab.gameObject, LayoutGroup);

            Connection.Connected += OnConnectedToMaster;
        }
        protected void OnConnectedToMaster()
        {
            RequestRooms();
        }
        protected virtual void RequestRooms()
        {
            if (!Connection.IsConnected)
            {
                Logs.Error("Tried to request rooms, but no connection was set");
                return;
            }

            var loadingPromise = Msf.Events.FireWithPromise(Msf.EventNames.ShowLoading, "Retrieving Rooms list...");

            Msf.Client.Matchmaker.FindGames(games =>
            {
                loadingPromise.Finish();

                Setup(games);
            });
        }
        public void Setup(IEnumerable<GameInfoPacket> data)
        {
            _items.Generate<TNGameListItem>(data, (packet, item) => { item.Setup(packet); });
        }
        public void OnRefreshClick()
        {
            RequestRooms();
        }
    }
}
```




