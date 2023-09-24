# Why produce a modkit?

Simply put, you can compile your editor uproject to produce binaries for its modules and its plugin modules. Including the uncooked content files, this allows for a modkit that:
- Allows for modders to test their mods in-game in-editor so do not have to package and boot the game for every test, with the ability to use the editor to create mods
- Does not expose any source code of the game or its plugins (unless you want to)
- Has automation utilities built into it that makes the modding pipeline far less complex, for example a button for packaging a mod, or a button for uploading a mod to a 3rd party mod hosting service

The only pre-requisite for modders to use a modkit like this, is the engine install for the version that the game uses. If the game uses a custom engine, then you will need to provide the engine fork as well.

## Why should I provide uncooked content?

As you can read about in the [Community Modkits](../CommunityModkits.md) section, modders have been able to create tools that can extract data from the cooked files, and convert them into raw, uncooked files within the editor. 

However, this is a very time consuming process, and can be error prone. If you provide the uncooked content, you are saving modders a lot of time and effort, and by doing this, you are also making it easier for them to create mods.

## IP (Intellectual Property) concerns

It is understandable that you may be concerned about modders being able to access your uncooked content, and potentially illegally use it for other projects. However, by choosing to develop your game on an engine where the source is freely available, you must know the risks of tools coming in to reverse engineer your game assets to essentially be ripped. If people are using your content for things illegally, it won't be from the any community modkit tool suite or your official modkits, but rather from the heap of other varying tools out there that make it trivially easy to do without even installing the engine. 

If you are seriously concerned of uncooked asset stealing for other/competing UE projects, you should therefore invest time into stripping your modkit of certain asset types that you don't wish to be exposed to modders, such as textures, meshes, animations, etc. The technical problem to solve here, is you need to maintain all references between assets. For example, if you strip a texture, any materials that use it will no longer compile. You can solve this by either:
- Providing an empty version of the asset (except animations, which can be stripped to a single frame) with the same package name, so that the references are still valid
- Replacing all references to a stripped asset with an engine default equivalent, such as the world grid material for materials 

If the above problem is too time consuming for you, and you still wish to provide a modkit, you could consider officially allowing modders to produce and distribute a community-made modkit using the community modkit tools, and then you can:
- Provide any source assets of types you don't mind sharing, such as blueprints (their graph are not reconstructed)
- Force modders to generate a "public project" where all texture, material, mesh and animation assets are replaced by the engine default equivalents
- Add your own editor plugins that help with the modding process, such as a button to package a mod, or a button to upload a mod to a 3rd party mod hosting service

## What if my game contains paid plugins from the marketplace?

If your game contains paid plugins from the marketplace, you may be concerned about modders being able to use them for free. 

- If it is a C++ plugin, then you should never include the source files without direct permission from the author, but the binaries are fine to include, as they are in the shipped game build anyway. 
- If it is a content only plugin, then you will need to make sure that you have permission from the plugin authors to distribute. If you don't, you will have to strip the plugin content and replace with dummy versions so that references in your game content are still maintained.

## Wouldn't allowing PIE be an issue where players can play the game for free?

This is a fair concern, but it is fairly easy to deal with this. It's up to you how you want to do it, but here are some ideas:
- Lock the modkit download behind a wall where only people who own the game can access it (e.g. seperate branch on Steam)
- Have a custom editor plugin that is always running, and when the player is in PIE, it will limit the play time per session to only a few minutes, then force close PIE. If they are needing longer, then it is fair to require them to package their mod and test in the actual game