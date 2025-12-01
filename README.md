A lot of cross platform games and applications on mac have absolutely atrocious latency, but not this demo.
you should see a near constant 60 fps with perfect os-application mouse syncronization while vsync is on. If not, tell me what you see and on what os version.
there can be A frame spike especially soon after launch, but it should go away. you can set frames in flight to 2 to remove the spikes altogether

the issue is that a lot of devs assume that waiting on a command buffer to complete limits the number of frames in flight, but that's not true at all, at least not after macos 10.15. So a while(!quit) game loop quickly enqueues 4 drawables and waits on them and the waiting is done when you ask for the next drawable (usually towards the end of your frame after you sampled your input), so in effect, you're always using last frame's input. Callbacks like CVDisplayLink helps with the timing and keeping your input fresh, but that alone won't do, you have to use [id<MTLDrawable> addPresentedHandler:^] to actually detect when the frame is displayed, then wait for an extra CVDisplayLink call if you want to skip the current frame (due to an undesirable number of frames in the swapchain queue)

this implementaton uses the pr request in SDL_gpu_metal.m I made in sdl3 to address the issue

[sliderrr.webm](https://github.com/user-attachments/assets/ba703e73-6439-4a74-959f-07986508bd80)
