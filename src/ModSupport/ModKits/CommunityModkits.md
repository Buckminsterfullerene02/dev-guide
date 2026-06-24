# Community Modkits
Since the engine source is freely available, it is not unreasonable to expect that modders can reverse a lot of things about the engine. Despite each engine version producing its own set of new challenges, the work of a handful of people has made a lot more possible. If you don’t want to provide any of your own modkits, it is highly likely that modders will produce one for you regardless of what you want. Of course, it depends on the conditions of modding, such as those settings covered previously, or engine version. 

## C++ Header Template Project

These are "base" projects that provide all the native class schemas without having to write any C++ by hand. This is the most common form of community modkits as they are very easy to create and update - it typically takes me under an hour to create a new base template for a new game.

There are two tools used to create these projects:
- [UE4SS UHT gen](https://docs.ue4ss.com/dev/guides/generating-uht-compatible-headers.html)
    - This method is older than the other, and takes a lot longer to create due to generating physical C++ header files on the disk and there are always several errors to fix where the UHT dumper falls short. 
    - It requires the project to be recompiled every time there is a game/header update
    - [Example for Deep Rock Galactic](https://github.com/DRG-Modding/FSD-Template)
- [Suzie](https://github.com/trumank/Suzie)
    - This method takes in a `.jmap` file (basically a JSON file containing info dumped from the running game process) and generates the class schemas at editor startup
    - It only takes a few seconds to generate so the editor startup time is hardly impacted
    - No recompiling required for updates - just update the `.jmap` file

Both methods have their pros and cons and trade blows on each other relating to limitations and generation bugs, but ultimately I think Suzie is the most useful as updating the project after a game update is as simply as loading the game and regenerating the jmap file from it and placing it back into the project. It takes only 5 minutes to update the project and since there is no compiling to do, errors encounted are always zero.

## Full Asset Content Project

Given enough time and patience, modders can and have generated full asset modkit projects for games - both as cooked and uncooked editor projects:
- Cooked editor modkits are created pretty much the exact same was as described in the developer modkits - How? section, but require all the games' engine changes to be replicated for the unversioned property serialization to match, otherwise cooked content just will not load in the editor correctly
- Uncooked editor modkits are created using tools that "decompile" the cooked game content, then "recreate" uncooked content from that information in the editor. This process always has been the most fickle be far and my modkit for Deep Rock Galactic is the only example that exists that is almost fully functional - but it was still very unstable 

## Examples

### Cooked editor modkit

The most complete modkit I've generated is for Subnautica 2, which you can find the repository for [here](https://github.com/Subnautica2Modding/Subnautica2-Project). 

It is a cooked editor modkit that is pretty much at the same standard that an official studio might create for their game, minus compiled game and plugin binaries, or developer editor tooling.

Here's the demo video for the project:

<iframe width="560" height="315" src="https://youtu.be/Zp8MTKuRoTw?si=5GjVmgcIGAb_ALkK" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

### Uncooked editor modkit

The most complete uncooked modkit I've generated is for Deep Rock Galactic, which you can find the repository for [here](https://github.com/DRG-Modding/Community-Modkit). 

It is a project containing tens of thousands of reconstructed assets of varying types from the game files to use for references in blueprint mods. There is a [massive mod](https://mod.io/g/drg/m/mission-content-randomizer) the size of an indie game that adds tons of new content to the game by using these references, created in a beta version of the modkit. It really is incredible what people can do with it!

This was hundreds if not thousands of collective hours of dev time 2 years by myself and others (named in the repository's credits). 

The developers of the game gave me permission to distribute the full modkit to the public, as long as users adhere to their UGC policy.

Here's the demo video for the project:

<iframe width="560" height="315" src="https://www.youtube.com/embed/0odKe6elOLQ?si=0610uj27leIQJ9vi" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
