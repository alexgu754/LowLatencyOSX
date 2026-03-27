A lot of cross platform games and applications on mac have absolutely atrocious latency, but not this demo. you should see a near constant 60 fps with perfect os-application mouse syncronization while vsync is on. If not, tell me what you see and on what os version. there can be A frame spike especially soon after launch, but it should go away. you can set frames in flight to 2 to remove the spikes altogether

the issue is that a lot of devs assume that waiting on a command buffer to complete limits the number of frames in flight, but that's not true at all, at least not after macos 10.15. So a while(!quit) game loop quickly enqueues 4 drawables and waits on them and the waiting is done when you ask for the next drawable (usually towards the end of your frame after you sampled your input), so in effect, you're always using last frame's input. Callbacks like CVDisplayLink helps with the timing and keeping your input fresh, but that alone won't do, you have to use [id addPresentedHandler:^] to actually detect when the frame is displayed, then wait for an extra CVDisplayLink call if you want to skip the current frame (due to an undesirable number of frames in the swapchain queue)

also you need to ensure you sample input AFTER acquring a swapchain or making sure a swapchain will be available, because calling next drawable will block and introduce an easily avoidable frame latency to your program.

AND! you must call SDL_GetMouseState(&mouseX, &mouseY); AND ImGui_ImplSDL3_NewFrame(); AFTER calling while (SDL_PollEvent(&event))) or equivalent event poller, putting them before the while loop looks innocent but will actually introduce 1 frame latency since you're actually getting last frame's mouse position. All in all thats FIVE frames of latency that probably a majory of applications have

if you a have a lot of cpu work, resampling input (doing poll events, then calling SDL_GetMouseState) helps a lot, so you can have fresh mouse position before doing ImGui. If you are unplugged or are on low battery mode / have a slow / overheated computer, it will introduce latency and something as simple as an imgui can take 5ms cpu time and you'll miss the displaylink so that makes this setup kind of difficult

this implementaton uses the pr request in SDL_gpu_metal.m I made in sdl3 to address the issue


https://github.com/user-attachments/assets/fa9cb4d1-31f5-45a9-87a3-ef030027dce8

