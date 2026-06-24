# Making a better ecosystem for making mods in your game

There are a few ways you can improve the quality of mod support:

- Don’t hardcode values in your C++. This is a bit of a no brainer, but you’d be surprised how much I’ve seen games do this

- Use [data driven gameplay](https://dev.epicgames.com/documentation/unreal-engine/data-driven-gameplay-elements-in-unreal-engine) programming style with data tables and other assets for providing values, as it is much easier to find and modify values than if they are scattered around the project, or worse, hardcoded in C++

    - If using data tables to drive data, then please create reflected C++ functions for data table modification (reading on data tables, adding new rows, modifying existing rows etc.) 

    - If using data assets to drive data, make sure you are mounting the asset registry of mods (if mods are as plugins, the engine does this natively) so that the asset registry of the game is picking up mod assets. Also make sure that your asset registry scans for assets of parent class or at worst directory (if mods are plugins) relative to the root folder of the game/plugin so that mods' data assets can be picked up without any extra work

- Provide `.pdb` files that provide modders with full stack traces and an easier time reversing game code to find out how it works

- Add plugins to game project on request, e.g., `SteamVR/OpenXR` if modders want to make a VR mod

- Provide nice blueprint functions in C++ blueprint function libraries or base classes such as:
    - Reading/writing strings from/to files (and making it clear what path roots are)

        ```cpp
        UFUNCTION(BlueprintCallable)
        bool WriteToPlainText(const FString& Filename, const FString& TextContent, FText& OutError, bool Append);

        UFUNCTION(BlueprintCallable, BlueprintPure)
        bool ReadFromPlainText(const FString& Filename, FString& OutTextContent);
        ```

    - Data table modification functions (reading on data tables, adding new rows, modifying existing rows etc.)
    - Allow adding persistent objects by constructing the object and putting it into an array in the game instance, and then initializing them after construction
    - OnMainMenu and OnLevelStart events that have an out parameter with the level that was loaded

- Provide delegates that fire when certain game events happen that you think mods would like to use

- Provide getters to references of central game systems if they don't already exist 

- Create a "middleware" interface consisting of reflected C++ headers that allow blueprints to interface with an internal C++ system (such as a bespoke tech tree system) or a paid marketplace plugin (such as the voxel pro plugin). This is highly sought after as developers can:
    - Avoid using C++ modding which may pose a security risk

    - Control exactly which parts of a system mods can access, and how
    
    - Provide the ability to mod previously "unmoddable" systems e.g. a plugin without reflected headers, so couldn’t be accessed via blueprints