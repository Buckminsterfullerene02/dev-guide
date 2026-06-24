# How to create a cooked editor modkit

As previously mentioned, due to the existence of UEFN, Epic Games have invested a lot into making the editor able to handle cooked content fairly well - and the later the engine version, the better it will be. 

Firstly, read up on the [Unreal docs for working with cooked content](https://dev.epicgames.com/documentation/unreal-engine/working-with-cooked-content-in-the-unreal-engine) in the editor to understand how it is in the vanilla engine.

As you can see, it is still quite limited. To maximise the potential of the cooked content in the editor, some engine changes will be necessary - but they are really no that complicated changes. Most of the changes are simply necessary to guard against editor code paths that aren't expecting cooked content.

## Engine changes

I will explain each engine change I had to make in UE5.6.1 for the Subnautica 2 modkit - what they are and how they work. Your results will vary depending on the engine version, but that is for you to figure out.

### Mounting game content containers (works with IoStore and non-IoStore games similarly)

So in the engine, there is an existing project startup command line flag `-UsePaks`. This flag allows you to directly mount container files to the editor.

Engine edit `-UsePaks` flag to always be enabled (optionally, flip it so that you can supply `-NoPaks` flag to disable mounting).

Now 







- setup project configs to not use any 
- make sure engine changes for loading cooked content are wrapped with WITH_EDITOR
- make sure gameplay tags are all defined in the project