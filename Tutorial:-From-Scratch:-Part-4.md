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

Finally, it should look like this:

![](http://i.imgur.com/9UGeDt1.png)



