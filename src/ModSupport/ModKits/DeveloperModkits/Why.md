# Why produce a modkit?

Simply put, you can compile your editor uproject to produce binaries for its modules and its plugin modules. Including the cooked (or uncooked) content files, this allows for a modkit that:
- Allows for modders to test their mods in-game in-editor so do not have to package and boot the game for every test, with the ability to use the editor to create mods
- Does not expose any source code of the game or its plugins (unless you want to)
- Has automation utilities built into it that makes the modding pipeline far less complex, for example a button for packaging a mod, or a button for uploading a mod to a 3rd party mod hosting service
- Has developer editor tools in that your team uses for helping with making content in the source editor, for example with automating tedious tasks

The only pre-requisite for modders to use a modkit like this, is the engine install for the version that the game uses. If the game uses a custom engine, then you will need to provide the engine fork as well.

## Cooked content?

Due to the existence of UEFN, Epic Games have invested a lot into making the editor able to handle cooked content well - and that gets better in each new engine version. 

With a few small engine changes, cooked content can act similarly to uncooked content in the editor - and importantly, still allows all references to exist in blueprints, widgets, animations, materials etc. 

I will go into a lot more detail about cooked editor in the [How to create an editor modkit](How.md) & [Cooked editor engine changes](CookedEngine.md) pages but using cooked content is very beneficial because:
- It keeps game content read-only - mods should not be directly modifiying game content as a general rule
- It keeps the project content size minimal - if you set up the editor to directly mount the cooked content from the game install files, there is no need for duplicate data on disk
- No compiling shaders required during editor startup or on cook, as it directly uses the compiled shader files when rendering materials - also means no need to create & distribute a shared DDC pak
- Editor startup time is very short - all uncooked content is loaded at startup, but cooked content is only loaded when needed (e.g. it or an asset that depends on it is opened or referenced), as well as no shader compilation required
- If you're using World Partition levels, the amount of loose files it generates is ridiculous and is not really fit to be handled by distribution fronts, so having it as part of the cooked content is nice
- Since you are using the very same cooked content from the game install, there is no need to worry about IP concerns if you are using marketplace assets/plugins - distribution as cooked content already has to be allowed by the licenses, and you aren't breaking that
- Your legal team (if applicable) would have a harder time justifying why not to allow a modkit to be created - no uncooked/source content, no source code, mounting content from game install (thus needing to own the game to use it)

There are a couple limitations to it:
- Engine changes are required to make it useful - so more effort & bigger distribution size (if not already distributing custom engine)
- Some asset types (animations, skeletal meshes, meshes, niagara asset etc.) are not copyable so mods cannot make copies to modify them in their mods - but this could probably be fixed with more engine changes that I do not cover in this guide

## What if I want to include uncooked (source) content?

No problem! Please do, and the editor is even easier to setup with uncooked content than cooked content, but is simply not a popular choice among studios due to IP/legal issues, hence why I am suggesting cooked editor as a more likely choice. I go into detail about making an uncooked editor in `How to create an uncooked editor modkit` page.

Uncooked content has the following benefits over cooked content:
- Content is not read-only (but still shouldn't really be edited directly by mods) and can be copied into mod content and edited to fit the mods' needs (cooked content can be done like this with some caveats I will get into later)
- Does not require engine changes to enable
- Includes all material shader code and blueprint code

## What if my game contains paid plugins from the marketplace?

If your game contains paid plugins from the marketplace, you may be concerned about modders being able to use them for free. 

- If it is a C++ plugin, then you should never include the source files without direct permission from the author, but the binaries are fine to include, as they are in the shipped game build anyway
- With cooked editor, you do not need to include source content, so you should not be breaking any licenses by including it in the modkit

## Wouldn't allowing PIE be an issue where players can play the game for free?

This is a fair concern, but it is fairly easy to deal with this. It's up to you how you want to do it, but here are some ideas:
- Configure the cooked modkit to mount the content containers directly from the game install folder. This will then require users to buy the game and have it installed to use the modkit
- Have an editor plugin that is always running, and when the player is in PIE, it will limit the play time per session to only a few minutes, then force close PIE. If they are needing longer, then it may be fair to require them to package their mod and test in the actual game