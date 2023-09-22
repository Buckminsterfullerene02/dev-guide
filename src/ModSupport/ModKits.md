# Modkits/SDKs
If you want a thriving mods community, you should provide tools to make things either possible at all, or just easier. While community created tools can cover many bases that developers won’t, it always makes sense for official tools to be created. 

While I create modkits for multiple games, I can see the scenario where I get burnt out and stop maintaining my tools. Even though I try to document as much as possible, if no one picks up the slack, entire modding pipelines can grind to a halt or be reset to square one. If the developers created official modkits, there is an obligation to keep things maintained and documented clearly so that the next developer can continue the work. Modkits may only need to be updated per major update, so if the game is no longer updated, then the final modkit will be in-line with the final game version.

But what sort of stuff could modders expect in a modkit?
- A shipping build of the editor, where C++ is compiled into binaries, and the uncooked assets are there... uncooked. This allows for Play In Editor mod testing, while also not allowing for the underlying C++ to be exposed. This is discussed in more detail in the [Developer Modkits](https://unreal-modding-library.github.io/dev-guide/ModSupport/ModKits/DeveloperModkits) section.

- A "middleware" interface consisting of reflected C++ headers that allow blueprints to interface with an internal C++ system (such as a bespoke tech tree system) or a paid marketplace plugin (such as the voxel pro plugin). This is highly sought after as developers can:
    - Avoid using C++ modding which may pose a security risk

    - Control exactly which parts of a system mods can access, and how
    
    - Provide the ability to mod previously "unmoddable" systems e.g. a plugin without reflected headers, so couldn’t be accessed via blueprints

- Code for the game’s engine fork if it has one. The best way to do this would be to have someone make a fork of the UE source code on GitHub and then upload their edits to that as a mirror of their own project’s one. Due to the UE EULA, the engine source MUST be distributed via an Epic Games verified path, such as a fork of the engine or via the marketplace

- Certain assets (ideally all of them) like blueprints that modders could modify directly in-editor. Modders can technically already obtain these from parsing cooked uasset files, but it is a lot of work and can be error prone

- Following on from the previous, animation assets such as animation sequence and skeletal mesh. In FBX format, if possible, but as uasset can suffice too. This saves a bunch of time ripping the assets from the game using various tools, which can also be wrong when a game uses a different forward axis for skeleton bones or other things 

## Creating good "conditions" for modding your game
Aside from modkits, there are a few ways you can improve the quality of mod support:

- C++ enums cannot be edited without help from an external tool, so use user defined enums where possible, or not at all

- Make as few major engine edits as possible – if you do, and you need to distribute your fork to modders, it doesn’t stop being a huge pain

- Don’t hardcode values in your C++. This is a bit of a no brainer, but you’d be surprised how much I’ve seen games do this

- Use data assets where possible – they are very easy to edit/add to/create frameworks from, and they make general project quality clean

- Add plugins to game project on request, e.g., `SteamVR/OpenXR` if modders want to make a VR mod

- Provide nice blueprint functions in C++ blueprint function libraries or base classes such as:
    - Reading/writing strings from/to files (and making it clear what path roots are)

        ```cpp
        UFUNCTION(BlueprintCallable)
        bool WriteToPlainText(const FString& Filename, const FString& TextContent, FText& OutError, bool Append);

        UFUNCTION(BlueprintCallable, BlueprintPure)
        bool ReadFromPlainText(const FString& Filename, FString& OutTextContent);
        ```

    - Allow adding persistent objects by constructing the object and putting it into an array in the game instance, and then initializing them after construction
    - OnMainMenu and OnLevelStart events that have an out parameter with the level that was loaded

- By extension of the above two, adding a reflected JSON library for blueprints is always amazing to have