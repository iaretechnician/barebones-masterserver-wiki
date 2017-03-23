### High CPU usage

If you are noticing a high CPU usage when running without renderer (batchmode / nographics), this is most likely because of the uncapped framerate.

Your CPU will try to run full-throttle to compute as many frames per second as it can (which can be a few thousands), which, most often, is unreasonably high.

You can limit the framerate like this (anywhere in your unity scripts):

`Application.targetFrameRate = 60;`

ℹ️ What's the recommended limit? Well, it depends on what you need. On most servers, I keep mine to 60, but I have a couple of servers running on 10 fps (they don't need to be very responsive).