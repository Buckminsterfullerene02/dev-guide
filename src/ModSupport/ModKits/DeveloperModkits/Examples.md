# Examples of a developer modkit

## Astro Colony

The Astro Colony official modkit (and its whole mod support) was developed following my direct advice and guidance, but also contains a few extra handy tricks up its sleeve that is worth mentioning, and has fed back into the creation of the developer modkits section of this guide (as I had not written it yet when I was advising them).

You can find the documentation for and the google drive download link for the modkit [here](https://docs.google.com/document/d/1ZDhyMEZzLPiHIUOYyHeiAVVUIPFvbwLul9WAgymHazI) (you don't need to own the game to download it, despite it having full PIE support).

The modkit uses plugin content for each mod. Here's an example of the content browser, where each mod folder is coloured beige:

[![Astro Colony content browser](../../../Images/ACContentBrowser.png)](https://cdn.discordapp.com/attachments/1109192354595876944/1155439585682264145/ACContentBrowser.png)

## Plugins

Astro Colony contains a number of free, custom and paid plugins. One of the paid plugins is the $350 [voxel plugin pro](https://www.unrealengine.com/marketplace/en-US/product/voxel-plugin-pro). The developer asked for, and got direct permission from the developers of this plugin to ship the binaries and content with the modkit so that PIE works and mods can use the voxel assets/classes. 

## Main blueprint actor

Inside of the mod, the main initialization blueprint can be created. There are three events that modders can use to control the game in specific moments:
- Init - should be used to configure game values and adding new recipes
- BeginPlay - when interacting with game actors or spawning new actors
- EndPlay - should be used to clean up the game state when the map is closed

## PIE time limiter

The modkit has a 20 minute PIE timer per session. However you can also quicksave and quicklod the session to continue it later while debugging mods.

[![Astro Colony PIE limit](../../../Images/ACPIELimit.png)](https://cdn.discordapp.com/attachments/1109192354595876944/1155439734781386832/ACPIELimit.png)

## Mod config file

Each mod is required to contain a mod config asset, which looks like this:

[![Astro Colony mod config](../../../Images/ACModConfig.png)](https://cdn.discordapp.com/attachments/1109192354595876944/1155439663654383666/ACModConfig.png)

## Create mod

[![Astro Colony mod config](../../../Images/ACCreateMod.png)](https://cdn.discordapp.com/attachments/1109192354595876944/1155439601524158474/ACCreateMod.png)

## Package mod

[![Astro Colony mod config](../../../Images/ACPackageMod.png)](https://cdn.discordapp.com/attachments/1109192354595876944/1155439711666577439/ACPackageMod.png)

## Upload mod

[![Astro Colony mod config](../../../Images/ACModUploader.png)](https://cdn.discordapp.com/attachments/1109192354595876944/1155439688992170014/ACModUploader.png)