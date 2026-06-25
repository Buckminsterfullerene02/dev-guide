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

The content browser does not directly mirror the contents of packages on disk or mounted - instead, it builds a virtual view of the packages known to it at editor startup or when refreshed due to actions from content browser (such as creating, deleting or renaming an asset). Since loose assets are there on disk at startup, it can find these files immediately. However, since the mounting happens later in the engine init than the content registry read, it is missing all those in the mounted container.

Thankfully, again the engine already provides a neat way to to do this - an editor startup commandline flag `-EnablePremadeAssetRegistry`. This looks for an `AssetRegistry.bin` file in the project root and then loads up the content registry with all packages from it. Simply supply your game's `AssetRegistry.bin` file in the project with this flag and you should be able to see all cooked content in the editor. Make sure that the asset registry file that is in the project root is always the same version as from the installed game files - as otherwise it may show assets in the content browser that do not exist in the game or not show ones that do.

In [this engine change](https://github.com/Buckminsterfullerene02/UnrealEngine/commit/806fd090cc5a66e5dac5caf9a25e7b00a27dc2fc#diff-3cfb9186423cddbddba9fc668dfc9a8c8d279d81920a79bd47e767a1f2f667aaR391), I do same as I did with `-UsePaks` -> `-NoPaks` flag - flip it to `-DisablePremadeAssetRegistry` so that `bUsePremadeInEditor` is true by default with the option to disable it if needed.

If there are still no cooked assets showing up in the editor, make sure you have these configs set in `DefaultEngine.ini`.
```ini
[/Script/UnrealEd.CookerSettings]
cook.AllowCookedDataInEditorBuilds=True
s.AllowUnversionedContentInEditor=1
```

I also found a bug that when deleting an asset in the content browser, the registry would refresh and "loose" all of the packages from the mounted container - because the refresh logic was simply only looking for packages on the disk - thus the cooked content would disappear. So to fix that I [created a helper](https://github.com/Buckminsterfullerene02/UnrealEngine/commit/806fd090cc5a66e5dac5caf9a25e7b00a27dc2fc#diff-3cfb9186423cddbddba9fc668dfc9a8c8d279d81920a79bd47e767a1f2f667aaR70) to check if an asset is from mounted container and then used it in the code paths related to regenerating the registry (also present in the same commit).

### Enabling all cooked blueprint references

This is arguably the most important part of modkit - when a modder is creating their blueprint logic, 99% of the time they will need to get references to a game asset, for example in:

- Casting
- Get all actors/widgets of class
- Getting/setting a property of a blueprint
- Calling a blueprint's function
- Binding to a blueprint's delegate

The main reason to provide a modkit is so that all the references for the mod are just there, readily available for the modder - no need for the modder to manually create dummy assets just to get their references.

In the vanilla engine, cooked blueprints are only referencable from blueprint code in asset list dropdowns such as on the get al actors of class dropdown. Any of the other referencing examples above aren't doable without an annoying workaround - creating a child blueprint of a cooked blueprint, which does a deep copy of the blueprint's component tree and saves it to an uncooked package on the disk. Since it's a child BP, defaults can still be accessed/modified as well as the copied component tree. However, it does not copy any of the properties, functions or events. So those still need to be manually dummied. Also, creating a child introduces some additional complexity in code as it's not actually the game blueprint they're referencing directly. 

The fix for this turned out to be insanely simple - a single if statement change. In a nutshell, the code that builds the actions database (which is the stuff that appears in the context menu when you right click in a blueprint graph) was that the package's `Class->ClassGeneratedBy` property was never null (it is for cooked assets), thus was going down a code path that would silently fail. Once the change is made, the actions database is built using a seperate code path that doesn't rely on `Class->ClassGeneratedBy` and then simply works.

[Engine change](https://github.com/Buckminsterfullerene02/UnrealEngine/commit/6c597b9adc8a2d4c277104206ab39516901f8483)

### Allowing all cooked assets to be openable

By default, trying to open a cooked asset will lead to a notification message saying something like "Asset not editable". Obviously this is not useful, so you need to change this to allow opening them. 

### Cooked niagara asset viewer

While the engine already provides relatively solid code paths for viewing cooked content for most asset types, one type that (as of 5.6) has no read-only viewer is niagara effect. Like blueprints and materials, the editor-only metadata (such as kismet node graph) is stripped from cooked assets. So when you try to open this asset, it will just crash, as the editor only has code paths for trying to directly load its metadata as if its uncooked.

Therefore, I decided to take a page out of the read-only blueprint code by implementing my own read-only viewer for niagara assets. This viewer shows all of the properties of the asset as well of each effect created inside of it. This is useful for providing more easily obtainable info about the asset in the editor rather than having to rely on third party tools like FModel (which is also much harder to read/understand than in the editor) - for stuff like copying the effect's behaviours or to modify at runtime.

[Engine change](https://github.com/Buckminsterfullerene02/UnrealEngine/commit/7454f17f665b570fe9a76aefe77b512e998f3e3a)


TODO
- setup project configs directories to never cook
- make sure gameplay tags are all defined in the project
