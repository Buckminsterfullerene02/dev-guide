# Developer Modkits

There is a certain way to produce a modkit that trumps every other way and is extremely powerful for modding, while **not** exposing the source code of the game.

This is an idea that is very obvious in hindsight, yet is something almost no one has done before. I really hope that more games in the future will do this!

## What is it?

Simply put, you can compile your editor uproject to produce binaries for its modules and its plugin modules. Including the uncooked content files, this allows for a modkit that:
- Allows for modders to play the game in-editor, with the ability to use the editor to create mods

- Does not expose any source code of the game or its plugins

- Has automation utilities built into it that makes the modding pipeline far less complex, for example a button for packaging a mod, or a button for uploading a mod to a 3rd party mod hosting service

The only pre-requisite for modders to use a modkit like this, is the engine install for the version that the game uses. If the game uses a custom engine, then you will need to provide the engine fork as well.

Then, in order to distribute it, you only need the `.uproject` file & following folders:
- Binaries (with or without `.pdb` files, up to you)
- Config
- Content
- Plugins (with the plugin binaries/content/configs)

You can zip this all up and distribute it via any method, for example a seperate Steam branch. As opposed to a "conventional" modkit, the file size is a LOT smaller, since you are not distributing the entire engine with it. I will also discuss the ways to reduce file size further in the [Keeping file size down](#keeping-file-size-down) section.

## Why should I provide uncooked content?

As you can read about in the [Community Modkits](https://unreal-modding-library.github.io/dev-guide/ModSupport/ModKits/CommunityModkits) section, modders have been able to create tools that can extract data from the cooked files, and convert them into raw, uncooked files within the editor. This is especially easy in UE5.2+ with the introduction of editor data. 

So, if you are concerned about modders being able to access your uncooked content, you should know that they can already do this.

However, this is a very time consuming process, and can be error prone. If you provide the uncooked content, you are saving modders a lot of time and effort, and by doing this, you are also making it easier for them to create mods.

## How do I do it?

Everything I describe here can be automated fairly easily, in order to make making new modkits simple for minor or major updates. I also recommend that you experiment with build flags to see what might work better for you.

First, you will need to make a copy of your game's project, as this copy will be compiled and built differently to the main project.

Now, you may need to change the build type to modular. This depends on how you want to setup your modding system. It does not matter if your game is built modularly or not. 

- Build modularly if you want to have each mod as its own module, which means that in the editor, the mod content will show up under the plugins section. This helps to keep mods organised and seperate from the base game content, however the downside is that it is more difficult to keep track of any base content assets that the mod is changing/overriding. This is what Astro Colony went for, so check [that example](#astro-colony) for more details.

- Do not build modularly if you prefer to have mods as a folder inside of the base game's content folder. It intergrates a lot more easily with the Pak loader plugin and follows existing modding conventions, however it is more difficult to keep track of mods and their content.

If you chose to build the editor modularly, but your game is **not** build modularly, you must use the `WITH_EDITOR` macro surrounding any code defining modularity inside of the `.Build.cs` and `.Target.cs` files. Alternately for your `.Target.cs` file for your project, you can avoid using the macro by doing the following instead:

```cs
public class MyGameTarget : TargetRules
{
    public MyGameTarget(TargetInfo Target) : base(Target)
    {
        Type = TargetType.Game;
        // Other target settings...

        if (Target.Configuration == UnrealTargetConfiguration.Shipping)
        {
            LinkType = TargetLinkType.Modular;
        }
        else
        {
            LinkType = TargetLinkType.Default;
        }
    }
}
```

Next, inside of your `.uproject` file, you must make sure that the second entry in the JSON is `EngineAssociation`, and you have that set to your engine version.

After you build the editor project, you can look at keeping the file size down, which I explain in the next section.

Finally, you can delete all the rest of the unused folders, and any content folders/files that you don't want to ship in your modkit, for example files you wouldn't even cook in the first place.

## Keeping file size down




## Extra features that could be provided within the modkit


## Examples of a developer modkit

### Astro Colony

#### Common issues

### UEFN