# Do it yourself
Since the engine core API exposes many utilities related with pak mounting and such, it isn’t really all that difficult to implement your own solution. The Pak loader plugin primarily builds upon the `FPlatformFile` & `FileWalk` APIs which is a good point to start at.

If you need something really bespoke, maybe more flexible than the other two methods, this is probably the path you should take. I’ll talk about it in more depth later, but the game Deep Rock Galactic had a requirement for being able to “hot reload” mods during runtime, which is not possible without writing engine modifications yourself. 

Be aware though that any DIY solution is prone to more subtle issues that people may not notice for a long time but may cause a lot of issues. For example, in Deep Rock Galactic, there was a bug in the hot reload system where mods were not always loaded in a consistent order and went undetected for a long time. When it eventually surfaced, modders realised that it was the root cause of other issues they were having that they couldn’t explain. 

There is an absolutely fantastic guide detailing how you may go about achieving this on the Unreal Engine forums [here](https://forums.unrealengine.com/t/primer-loading-content-and-pak-files-at-runtime/536669/7). I highly recommend checking it out as it explains the process in a lot of detail and I completely agree with the way the implementation has been done through the use of plugin content. 

Here's another interesting project that's trying to do it for UE5: [UE5 Mod Manager](https://github.com/paterron/UE5MMExample). Definately some interesting code to look at in there, as the author has done a lot of research into the topic himself.

There is also the new UE5.3 ["extensible gameplay framework"](https://docs.unrealengine.com/5.3/en-US/game-features-and-modular-gameplay-in-unreal-engine/) sytem that's very interesting for mod support. I wonder how much can be done there. But if I were you, I wouldn't try to go too crazy on the new features, as it may go deep into unfamiliar territory and you may end up with a suboptimal mod support solution. But some research into it can never do harm.