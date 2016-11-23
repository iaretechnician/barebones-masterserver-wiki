This page will contain important warnings, for which I couldn't find a proper place.

## Enable Builds To Run In Background

⚠️ 🔴 Make sure to build your game so that it can **run in the background** - it's necessary for any game that needs networking. Otherwise, you might experience some nasty glitches. You'll find the setting in **File > Build Settings > Player Settings > Run In Background**

## Memory Leaks on Linux VPS

You might notice some memory leaks when running your servers on a Linux machine. This is due to a number of issues with **Unitys new UI system** (my guess). In production, it's recommended to destroy all of the **Canvas**'es. After destroying them, memory leaks should be fixed.

From the version V1.02, I've included a script called ObjectDestroyer, which destroys objects it's attached to, but only if you run the server with "`-bmDestroy`" argument.