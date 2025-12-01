a lot of cross platform games and applications on mac have absolutely atrocious latency, but not this demo.
you should see a near constant 60 fps with perfect os-application mouse syncronization while vsync is on. If not, tell me what you see and on what os veriosn.
there can be A frame spike especially soon after launch, but it should go away if you set frames in flight to 2

the issue is a lot of devs assume that waiting on a command buffer to complete limits the number of frames in flight, but that's not true at all, at least not after macos 10.15. so a while(!quit) game loop quickly enqueues 4 drawables and waits on them. Also callbacks like CVDisplayLink helps with the timing and keeping your input fresh

[sliderrr.webm](https://github.com/user-attachments/assets/ba703e73-6439-4a74-959f-07986508bd80)
