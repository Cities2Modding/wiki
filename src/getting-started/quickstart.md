# Quickstart

<div class="warning">

This guide is specifically meant for people experienced with modding. If you're looking for document suitable for beginners, try the [Longer Start](./longer-start.md) guide instead. 

</div>

## Prerequisites

- Cities: Skylines 2 acquired via [Steam](https://store.steampowered.com/app/949230/Cities_Skylines_II/) or [Game Pass](https://www.xbox.com/en-US/games/store/cities-skylines-ii-pc-edition/9PGZ346PSLN0). No other versions are supported at the moment.

## Installing BepInEx

- Download [BepInEx 5.4.22](https://github.com/BepInEx/BepInEx/releases/tag/v5.4.22)
- Extract directory
- Copy extracted directory to your Cities: Skylines 2 installation directory
    - You should end up with a `BepInEx` directory and the `doorstop_config.ini` next to the `Cities2.exe` binary

## Installing Mods with Thunderstore Mod Manager

- Install the [Thunderstore Mod Manager](https://www.overwolf.com/app/Thunderstore-Thunderstore_Mod_Manager)
- Select `Cities II` when running the mod manager
- Now every mod you install via the mod manager should be automatically installed into the right directory
- Make sure you launch the game via the `Modded` button in the top right, as otherwise Cities: Skylines 2 won't load any mods

## Installing Mods manually

- Each mod you want to install should be in it's own directory in `BepInEx/plugins`.
- If you install a mod called `CoolMod`, you should end up with the following structure: `Cities Skylines II/BepInEx/plugins/CoolMod/CoolMod.dll`