# Extra features that could be provided within the modkit

Even if you are not shipping your modkit with C++ modding support, it is still beneficial to include the reflected C++ headers folders for your modules and plugins. This allows for modders to see what they can use for their blueprints, and can additionally be used by UnrealLink to provide a smoother experience for experienced modders.

Additionally, there are a few other features to consider when producing your modkit:
- Forced reflection of headers
- Create mod utility
- Overriding game content
- Support for modding of data driven loaded content
- Mod configuration files
- Package mod feature
- Upload mod feature

## Forced reflection of headers

If you recall back to the basis of modding -> blueprints page, there was a section that discussed [force reflection](../../../Basis/Blueprints.md#force-reflection) where modders can access more C++ than the developers can based on changing the flags of the reflection macros. This is a very powerful feature that can be used to allow modders to do extra cool things, with the only downside being that in their editors they can see a lot of extra context to search through. 

However, in your modkit project, if you are providing a shipped editor build, modders are forced to stick with whatever reflection macros you have set in your project header files. This can be bad in two ways:
- It limits what modders can do with blueprints

- If modders have created mods in a community modkit with forced reflected headers previously, and they migrate to your modkit that don't, they will find themselves with a lot of compile errors and suddenly a lot less flexibility of what they can do

Therefore, it is recommended that you take steps to provide a way for modders to use this feature. There are a few options:
- If you are providing an custom engine patch, you can patch out editor visiblility checks for reflection macros and force everything to always be visible

- You can implement runtime access transforms (a fancy term for hot reloading) which modders can specify in their mod's config files, without modifying the header files. There is an existing system developed for Satisfactory modding, which you can find documentation for [here](https://docs.ficsit.app/satisfactory-modding/latest/Development/ModLoader/AccessTransformers)

- Run your own automation that modifies the reflection properties of all of your games' headers to maximum visibility before compiling your modkit project. In the vast majority of cases, this would be iterating through each header file and looking for reflection macros, and setting the flags to the most visible per macro, making sure you don't replace other important flags such as `BlueprintAuthorityOnly` or `NetMulticast`. You can see an example of what the "most" visible macros are by looking through [these example forced headers](https://github.com/DRG-Modding/FSD-Template/tree/main/Source/FSD/Public)

- Use the [UE4SS UHT generator](https://docs.ue4ss.com/guides/generating-uht-compatible-headers) to generate your project's headers with everything forced reflected (tested on and works UE4.22 - UE5.3). This is what community modkits use to get theirs. Then compile for binaries and drop your content into the content folder and continue your modkit development as normal, and then you can ship with C++ source files since they contain no code implementations. However, the big downside is that PIE will not work and it's not the easiest tool to use nor automate

## Create mod utility

If you are using plugin content to seperate your mods and your game content, you can provide an editor utility that allows modders to create a new mod with the correct folder structure, starting files (such as mod config and initialization blueprints etc.), and typical UE plugin information such as author, version, description etc.

## Overriding game content

Although directly modifying base game content is not recommended, it can sometimes be beneficial to provide an easy way to directly override entire assets during runtime, such as model, material or texture replacement mods. This can then bring visual modding away from conventional methods that involve a lot of manual work, into your modkit ecosystem, where mods can be managed by you more easily.

Modders could be able to add a configuration item in the mod configuration file (if you choose to have one, more about that in [its section](#mod-configuration-files)) that specifies game objects to override.

There are two ways to do this (but of course you could implement your own system if you find a better one) with their own pros and cons:
- Overriding the base game asset during packaging/in the mod pak file, by editing the base game asset, and then specifying the assets you have edited in the config file. Then, during packaging, the modkit project can package that asset into the pak file to allow it to override the base game asset like in a normal pak patching mod. An example modkit that uses this is the Astro Colony one, which you can read about more in the [examples page](./Examples.md#astro-colony). The downside to this are:

    - It does not work if you have the packed content as those assets cannot be saved

    - It is confusing for modders to understand that they need to edit the base game asset and specify that they have edited it in the config file

- Add base game objects that they want to override as the first value and the mod object they want to override it with as the second value to a suitable data structure (e.g. pair) in the mod config file. The game can then override the asset during mounting or loading, and has the upside of allowing users to have finer control over what assets get overriden in the case of mod conflicts or similar. The downside is that it is far more work for the game to implement

## Support for modding of data driven loaded content

Some projects use data driven loading to do stuff such as load tabs in a widget, or load a list of items in a shop. If you can support modding this by allowing modders to add their own data assets to any loading system, it could be very powerful. 

For example, you could have a specific subdirectory in your game content folder that is scanned for data assets with the class `AShopItem`. In-game, these shop items are loaded into a dynamic widget for the shop. Modders can then add their own shop items by creating a data asset with the class `AShopItem` and adding it to their mod content. In the mod configuration file, they could specify the game to additionally scan for these shop item data assets in the mod content folder.

## Mod configuration files

Config files can be used to specify extra mod information related to mod assets. For example, this may include the ability to set game objects to override, scanning for extra data assets to load, setting an in-game mod icon to a texture object, etc. There's a realm of oppurtunity for possible features to add here, depending on what you plan on doing.

## Package mod feature

Simply put, this can be a button in the editor that allows the modder to select the mod that they would like to be packaged and the output directory to do so. The implementation depends on what mod configurations you have (e.g. if you are overwriting game objects at package time), your pak settings (e.g. use io store if your game is) etc.

## Upload mod feature

Like the package mod feature, this can be a button in the editor that allows the modder to directly upload their mod from the editor to your 3rd party mod hosting service of choice. The extra fields to fill out can be specified in this utility, depending on the service you are using. 