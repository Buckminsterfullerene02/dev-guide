# What is this?
When I first started talking to game developers about modding in Unreal Engine, I realised that there are both misconceptions and a complete lack of knowledge of the field. My goal is to inform both developers and modders of all the possibilities that modding can offer. Only known and used methods will be covered, which means that I will not explore potential options that don’t exist yet, such as ones covered in [this post](https://forums.unrealengine.com/t/modding-data-driven-thoughts-how-to-best-accomplish/65495).

If you are reading this with a negative perspective of modding, I encourage that you keep an open mind and read at least the overview section before the deep dive into everything there is to know. If you know that you want to add mod support, great! Feel free to skip the overview section and go straight on to the good stuff.

*\- Buckminsterfullerene*

2026 update: I would like to draw your attention to this excellent talk by the head of engineering at [mod.io](https://mod.io/) which discusses many of the points covered in this guide and their own insights.

<iframe width="560" height="315" src="https://www.youtube.com/embed/qb0_ypUqnxE?si=U8jqfws3jGdTrRww" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

# Credits
- Sbsce from [Stonebrick Studios](https://store.steampowered.com/app/619500/cyubeVR/) for providing cyubeVR’s mod loading code
- Archengius from [Coffee Stain Studios](https://www.coffeestainstudios.com/) for explaining modular builds, helping with modkit creation processes, and generally knowing an insane amount about backend UE workings
- JonasHR from [Ghost Ship Games](https://ghostship.dk/) for insight into the internal Deep Rock Galactic mod system
- Daniel from [Minakata Dynamics](https://minakatadynamics.co.jp/) for being an awesome guy to bounce ideas back and forth with
- [Narknon](https://github.com/narknon) for having almost every 4.15+ engine source downloaded to provide information quickly
- Members of the Unreal Engine Modding [Discord](https://discord.gg/unreal-engine-modding-876613187204685934) for information tidbits

# Where to find me
- [Discord](https://discord.gg/unreal-engine-modding-876613187204685934) - `buckminsterfullerene` (please join the Discord first so I know where you are coming from)
- [GitHub](https://github.com/Buckminsterfullerene02)
- [Epic Games Developer Forums](https://dev.epicgames.com/community/profile/4an3/bobby459001)

# Versions
- 0.1 - 2023-01-20 - Initial release of first draft
- 0.2 - 2023-01-21 - Markdown pass, rewrote DLL injection section
- 0.3 - 2023-08-05 - Started guide about developer provided modkits
- 1.0 - 2023-09-24 - Added most detail to developer modkit section, reworked parts of the guide to be updated with new information/methods/engine versions
- 1.1 - 2024-02-15 - Added ContractorsVR case study, added new useful resource links where necessary, fixed some poor wording
- 1.2 - 2024-03-06 - Moved Astro Colony case study to match the format of the document
- 1.3 - 2024-09-25 - Update links
- 1.4 - 2026-05-05 - Don't recommend turning off `Use IoStore` setting for UE5 (because community tooling to mod IoStore files exist now), changes to the basics since last edits, added Whiskerwood case study, added Captain of Industry case study
- 1.5 - 2026-06-24 - Overhaul guides related to modkits - write about cooked editor

*No AI was used in the writing of this document*