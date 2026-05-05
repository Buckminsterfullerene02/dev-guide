# Whiskerwood

[Whiskerwood](https://store.steampowered.com/app/2489330/Whiskerwood/) is a cute city builder game built in UE5 and had excellent mod support from day 1 of its early access release with minimal effort from only one of the developers and with much room to grow the support in the future - it wasn't sure if modding would take hold in the game, so it was best to not put too much resources into making it feature complete at the very start. I wanted to write about Whiskerwood because it has some unique solutions to the mod support problem.

## Mod loading

The lead programmer on Whiskerwood decided to go with the simplest form of mod support - native blueprint mod loading. This was achieved in mere hours using the pak loader plugin as discussed previously. There are hooks for loading blueprints of specific names inside the mod folder (mods are not packaged as DLC in this game) based on when a mod wants to be loaded:
- `BP_Startup` - This blueprint is spawned by the game the first time the game loads into the main menu.
- `BP_MapLoad` - This blueprint is spawned by the game while loading into a save. 
- `BP_MainMenuLoad` - Triggered after `BP_Startup` but unlike startup, it will be triggered every time the main menu loads, not just the first time.

There are certain reasons that each hook is useful for, more detail on that can be found in the [docs](https://github.com/Whiskerwood-Modding/Whiskerwood-Project).

## Data table modding & mod API

Whiskerwood is largely developed using the data driven gameplay methodology, which lends itself largely to modding, as many mods can be made simply by changing some data table values. However, functionality to modify data tables are not natively moddable by blueprints, largely because data tables are usually meant for being read-only and loaded in at game load. 

Thankfully, Whiskerwood provides a [mod API](https://github.com/Whiskerwood-Modding/Whiskerwood-Project#modapi) in the form of reflected functions, delegates and properties. Some of these are related to reading and modifying data tables, e.g:

```cpp
// Read the value of a data table entry. See modapi's ListDataTables to get an updated listing of exposed data tables
UFUNCTION(BlueprintCallable, meta = (WorldContext = "worldContext"), Category = "ModAPI")
    FString ReadDataTableValue(class UObject *worldContext, FName datatableName, FName rowId, FName columnName);

// Write the value of a data table entry. See modapi's ListDataTables to get an updated listing of exposed data tables
UFUNCTION(BlueprintCallable, meta = (WorldContext = "worldContext"), Category = "ModAPI")
    bool WriteDataTableValue(class UObject *worldContext, FName datatableName, FName rowId, FName columnName, FString valueStringified);

// Add an empty row at the rowId. Does nothing if the row already exists
UFUNCTION(BlueprintCallable, meta = (WorldContext = "worldContext"), Category = "ModAPI")
    bool AddDataTableRow(class UObject *worldContext, FName datatableName, FName rowId);
```

These three functions make modding so accessible to the game for beginners as some mods (outside of the project setup which is a process in of itself) take 5 minutes to make as they are one or two function calls to make the functionality. 

Now, you may be wondering, if a mod is changing a data table that is usually read on game initialisation, how are the modded values actually loaded in? There are two ways this is made possible, and Whiskerwood uses the first:
- Make the data table edits in the main menu and read in the data table values when the game level itself is loaded (this is why `BP_Startup` exists)
- Load the mods before any of the logic that reads the data tables actually runs (this can be done on top of the first but is more technically challenging to achieve)

The mod API is useful in other ways - it provides delegates for some events that fire when certain actions occur that mods may want to use, getters for core game system references and other functions and utilities.

## Mod options

Whiskerwood provides native support for mods having their own settings which are found under a "Mods" tab in the settings menu - which did not take much work on the development side to add, as the settings menu was already modular.

This works by a mod registering its own option with a specific "Option Id" (some unique value such as modname-optionname), display name, description, default value and selectable values. At the beginning, the settings only had support for a multi-select UI component, but designed so that later on it may support new option types like sliders or colour pickers. 

The mod binds on a delegate in the mod API `onOptionChanged` which fires when any game setting is changed. If a mod wants to specifically check if one of its mod option Id's has changed, then it is as simple as doing a string comparison on the event's changed Id. This means that mods can also check for any other game or mod options within the same event.

## Mod localization

The Whiskerwood mod API handles mod localization in an interesting way. In the game, there is a data table for each language containing columns for a locale key and its localized string. Any other data table that wants to have a localized string just puts a locale key in it. 

So when a mod wants to add localization support, it simply has to call a single mod API function to add all of its locale keys and their translations (it is recommended to have a function called once per mod as the whole UI needs to be refreshed to use the mods' strings).

Alternatively, if a mod doesn't wish to provide localizations in all supported languages (and if more languages are added in the future they won't be supported), the mod can simply provide English strings (which are the fallback for other languages). Then, the architecture exists so that there is one mod per language that adds localization support for ALL mods.

This design is based on the frustration that many games end up with multiple versions per mod, each with their own localized strings, which brings about all sorts of problems, like each version of the mods getting out of sync, having to maintain multiple mods at once, mod author becoming inactive and localization never happening.

So, instead of ending up with multiple mods per mod, or mods with no way to localize, there should only ever be a maximum of the number of supported languages in the game mods (e.g. 15 mods), each providing localization for all other mods. 

Also, the language localization mods don't hardcode strings inside the assets - they read in localizations from text files in the mod's directory, so the average user can easily provide their own localizations and then share them with the wider community to either get the mod updated or for others to have more up to date files.

I wrote a guide for this system [here](https://github.com/Whiskerwood-Modding/Whiskerwood-Project/blob/main/Docs/Localization.md). I hope to see clever solutions like this in other international games.