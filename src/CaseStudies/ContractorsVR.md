# Contractors VR
I only discovered this game after I had written the whole guide, and it surprised me that it contained mod support similar to my own research. 

[Contractors](https://store.steampowered.com/app/963930/Contractors/) is a VR team-based shooting game built using a custom UE4.27 engine version. The game has incredible mod support that's way ahead of any other non-modular game (so excluding Satisfactory) I've seen (at the time of writing). Mods built for the game include [Team Fortress VR](https://mod.io/g/contractors/m/tf2-vr) with 2.3 million downloads and [Clone Wars](https://mod.io/g/contractors/m/clonewars) with 3 million downloads. This game would easily be dead by now if it weren't for its mod support, making it a textbook example why mod support is so important to keep games alive. These mods do exactly what you expect, but how have they been done knowing the difficulty of conventional UE modding?

## The modkit
The developers of Contractors have provided a [modkit](https://github.com/kcmonkey1992/ContractorsVRModKit/releases) that is a release of their game UE project on top of their custom engine version. It means that modders can play the game in-editor to test their mods. The modkit is distributed through a GitHub release on a fork of the UnrealEngine respository so that the distribution of the engine is legal. The combined download is 17.6GB which on its own is less than a default install of UE4.27, so they've done a good job of keeping the size down.

Upon first opening the modkit, modders are loaded into a map that contains a bunch of useful information about each main asset system and how they can be used. It's kind of like a mini-tutorial within the editor where modders can click on the actors and view the code to see how they work in more detail. They also describe how to setup basic systems such as the round timers, game mode, player controllers, game state etc. 

![Opening level](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/9e2dc294-de1c-4566-8489-31f3eee783f3)

You can also see three new buttons in the toolbar:
- Modio
- Create Mod
- Package

### Modio

The modio button opens a window for modders to log in with their mod.io account. From there, they can upload any mods created and packaged directly to mod.io without having to go into the site itself. In this example since I've not packaged my test mod, it's showing missing file error.

![Modio](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/b4b21632-8a41-4e0e-b794-d8db9f708315)

### Create Mod

The create mod button opens a window that asks modders to select the type of mod they'd like to make and the various mod info such as name, author, description etc. Basically an edit of the create plugin window, since the mods are created as plugin content.

![Create mod](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/8cde9428-f93a-4601-b8ad-598257470a93)

When a mod is created, it is added as a plugin to the project. Depending on the type of mod created, there are a few different assets that are created. As a base, the mods will create a data asset that describes the mod such as the display name of the mod, a thumbnail texture, the custom loadout file or the custom map file.

![Map Mod Data](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/964dcc69-2bc2-430a-9e86-2eadddabe923)

![Loadout Mod Data](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/8920817f-1050-4e4a-8fd2-17e0bd9af02e)

### Package

The package button requires modders to select the workspace to package their mods. Then, upon selecting the mod they wish to package, a window will appear that asks them to select their supported platform, and then will package for that platform. I suggest testing out packaging a mod to see how it works - there are commands in the output window and you can see what file structure they've opted to use.

![Package mod](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/d512620a-c35b-4fb7-aab8-c9cc815b235d)

This is the kind of extra seamless integration that I'd expect all modkits to provide.

If you want to dig into this modkit yourself, the main plugin responsible for the in-editor features is `Zomboy_Plugin`, and of course only the Binaries are provided, so you can't see the source code. 

I don't own the game so I can't comment on the in-game mod integration.
