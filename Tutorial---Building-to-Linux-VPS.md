(Quick write-up before I forget what I did)

We are using the QuickSetup demo for this tutorial. It has everything needed to get started with minimal work.

##Step 1:
Find your **VPS IP address**. You should be able to find it in the client area of the site you registered at.

You need to put this number in several places in the project. You can either edit it directly (may get overwritten when the assets are updated) or you can copy the QuickSetup directory and edit it there.

First change: Open the **Client** scene. Find the "ConnectionToMaster" GameObject. In the inspector, find "Server IP" and put the **VPS IP address** in there.

Second change: Open the **GameServer** scene. Do the same as above.