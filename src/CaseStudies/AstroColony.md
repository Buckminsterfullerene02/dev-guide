# Astro Colony

The Astro Colony official modkit (and its whole mod support) was developed following my direct advice and guidance, but also contains a few extra handy tricks up its sleeve that is worth mentioning, and has fed back into the creation of the developer modkits section of this guide (as I had not written it yet when I was advising them).

You can find the documentation for and the google drive download link for the modkit [here](https://docs.google.com/document/d/1ZDhyMEZzLPiHIUOYyHeiAVVUIPFvbwLul9WAgymHazI) (you don't need to own the game to download it, despite it having full PIE support).

The modkit uses plugin content for each mod. Here's an example of the content browser, where each mod folder is coloured beige:

![Astro Colony content browser](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/15b5630d-4adf-4f0b-bae3-e7a1ff960f1b)

## Plugins

Astro Colony contains a number of free, custom and paid plugins. One of the paid plugins is the $350 [voxel plugin pro](https://www.unrealengine.com/marketplace/en-US/product/voxel-plugin-pro). The developer asked for, and got direct permission from the developers of this plugin to ship the binaries and content with the modkit so that PIE works and mods can use the voxel assets/classes. 

## Main blueprint actor

Inside of the mod, the main initialization blueprint can be created. There are three events that modders can use to control the game in specific moments:
- Init - should be used to configure game values and adding new recipes
- BeginPlay - when interacting with game actors or spawning new actors
- EndPlay - should be used to clean up the game state when the map is closed

## PIE time limiter

The modkit has a 20 minute PIE timer per session. However you can also quicksave and quicklod the session to continue it later while debugging mods.

![PIE limit](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/720169f4-00c7-4fdb-b3d2-7fa2c1a23b13)

## Mod config file

Each mod is required to contain a mod config asset, which looks like this:

![Astro Colony mod config](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/59330bc4-3212-47d7-aafe-42f160a4ec17)

Inside of it, you can:
- Set game objects you've edited to override the base game objects during packaging
- Allow modders to add their own data assets to any loading system
- Set the in-game mod icon to a texture asset
- Specify details for any extra tabs for the mod hub

## Create mod

![Astro Colony create mod](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/303f7ce0-0fbe-4cd2-ac70-c839c4b4b3ea)

## Package mod

![Astro Colony package mod](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/e026c185-caab-46c2-a070-4134cb71d91d)

## Upload mod

![Astro Colony upload mod](https://github.com/Buckminsterfullerene02/dev-guide/assets/84156063/d132403d-02b1-4476-ac60-2ff1abdd8b23)
