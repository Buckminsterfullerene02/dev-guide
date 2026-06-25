# How to create a cooked editor modkit

As previously mentioned, due to the existence of UEFN, Epic Games have invested a lot into making the editor able to handle cooked content fairly well - and the later the engine version, the better it will be. 

Firstly, read up on the [Unreal docs for working with cooked content](https://dev.epicgames.com/documentation/unreal-engine/working-with-cooked-content-in-the-unreal-engine) in the editor to understand how it is in the vanilla engine.

As you can see, it is still quite limited. To maximise the potential of the cooked content in the editor, some engine changes will be necessary - but they are really no that complicated changes. Most of the changes are simply necessary to guard against editor code paths that aren't expecting cooked content.

## Engine changes

I will explain each engine change I had to make in UE5.6.1 for the Subnautica 2 modkit - what they are and how they work. Your results will vary depending on the engine version, but that is for you to figure out. 

Note that all modkit-related engine changes should be wrapped with WITH_EDITOR compiler guards so that the modkit changes don't exist in the game. While most of my engine changes are already doing this, one thing most changes are not taking into account is operability between a modkit editor and a source/non-modkit editor - as modders don't have access to the source editor so obviously there is no need to support anything but the modkit editor. 

### Mounting game content containers (works with IoStore and non-IoStore games similarly)

So in the engine, there is an existing project startup command line flag `-UsePaks`. This flag allows you to directly mount container files to the editor.

Engine edit `-UsePaks` flag to always be enabled (optionally, flip it so that you can supply `-NoPaks` flag to disable mounting).

While the engine does already support mounting containers from within the project, there are a couple drawbacks:
- It expects the container files to be at a relative location to the engine install. You should change this behaviour anyway though, as if you are distributing an installed engine build seperately, then the user may choose to install the engine to a different location, thus causing it to break
- You would need to either distribute your modkit project already containing the game content (inflating download size massively) or have some utility to copy the game content from the game into the project location (extra hoops, slow to copy, duplicate data)

So what I did is to make an engine change in `IPlatformFilePak.cpp` that reads in a txt file in the project root containing the path to the game install directory. It's a very simple to ask the user to supply the path manually during modkit setup, or if you had a method of reliably finding the game install location you could have such an algorithm in the engine change and then fallback to a txt file in case it fails. 

[Example change](https://github.com/Buckminsterfullerene02/UnrealEngine/commit/b0bd87c904ee5113a9d97dfd66bb0928f4406b06?diff=unified)

When reading through this change you may notice some code relating to load priority...

### Prioritise loading loose files over mounted containers

In your project you may have some loose game files that work better as loose/source files than used as cooked files. Example of these in Subnautica 2 modkit:

- Source .ufont files - (at least with IoStore) these do not resolve correctly as cooked files only as the ufont files are stored in a seperate container path, so I extracted the ufont files from the game's pak file and placed them directly into the project content folder under the correct directory path and names. If you just load from containers, any widgets or text using the cooked font files will look like `[A][A][A][A][A]`.
- FMOD banks - FMOD bank files are looked up as non UFS and as such are located as loose files in the game install folder, not in packaged containers. FMODStudio plugin then unpacks these banks at the first editor startup into the correct folders, as source assets. Therefore, the loose assets need priority over the cooked, non-working ones in the mounted containers (note that I did need to fix a bug in this copy process related to mounted containers [here](https://github.com/Buckminsterfullerene02/UnrealEngine/commit/806fd090cc5a66e5dac5caf9a25e7b00a27dc2fc#diff-3fa090adc93025c511740f2328f16c9b871189821dc723e796c0d2830b1f78c6))

You may have other loose assets that you define in your project as "Directories to package as non UFS" such as movies, textures, animations, models (if using Interchange plugin pipeline). 

Luckily, the engine already provides a way to do this: [`bLookLooseFirst`](https://github.com/Buckminsterfullerene02/UnrealEngine/commit/806fd090cc5a66e5dac5caf9a25e7b00a27dc2fc#diff-f35e4316bf0ccd6cbb45208a24228441b73e9c2aff719932f621983ddd99d574). I hardcoded this to always be true in the editor as there is no reason for it not to be as far as I can tell.

Once you've made these changes, you may notice that there are no assets showing up in the content browser (aside from any loose ones)...

### Enable premade asset registry

The content browser does not directly mirror the contents of packages on disk or mounted - instead, it builds a virtual view of the packages known to it at editor startup or when refreshed due to actions from content browser (such as creating, deleting or renaming an asset). Since loose assets are there on disk at startup, it can find these files immediately. However, since the mounting happens later in the engine init than the content browser registry read, it is missing all those in the mounted container.

If there are still no cooked assets showing up in the editor, do you have these configs set in `DefaultEngine.ini`?
```ini
[/Script/UnrealEd.CookerSettings]
cook.AllowCookedDataInEditorBuilds=True
s.AllowUnversionedContentInEditor=1
```

- setup project configs to not use any 
- make sure engine changes for loading cooked content are wrapped with WITH_EDITOR
- make sure gameplay tags are all defined in the project
