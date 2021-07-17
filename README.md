# Sonic Robo Blast 2

[![Build status](https://ci.appveyor.com/api/projects/status/399d4hcw9yy7hg2y?svg=true)](https://ci.appveyor.com/project/STJr/srb2)
[![Build status](https://travis-ci.org/STJr/SRB2.svg?branch=master)](https://travis-ci.org/STJr/SRB2)
[![CircleCI](https://circleci.com/gh/STJr/SRB2/tree/master.svg?style=svg)](https://circleci.com/gh/STJr/SRB2/tree/master)

[Sonic Robo Blast 2](https://srb2.org/) is a 3D Sonic the Hedgehog fangame based on a modified version of [Doom Legacy](http://doomlegacy.sourceforge.net/).

## Dependencies
- NASM (x86 builds only)
- SDL2 (Linux/OS X only)
- SDL2-Mixer (Linux/OS X only)
- libupnp (Linux/OS X only)
- libgme (Linux/OS X only)
- libopenmpt (Linux/OS X only)

## Compiling

See [SRB2 Wiki/Source code compiling](http://wiki.srb2.org/wiki/Source_code_compiling)

## Disclaimer
Sonic Team Junior is in no way affiliated with SEGA or Sonic Team. We do not claim ownership of any of SEGA's intellectual property used in SRB2.

I (flarn2006) take no responsibility for any damage to your save data or computer. In addition, while this version does allow you to use arbitrary mods in multiplayer games, I don't condone doing so in a manner that gives you an unfair advantage or otherwise violates the rules of the server you're playing on. If you choose to do so anyway, you take sole responsibility for the consequences of your actions.

## About this fork
This is my (flarn2006) personal version, containing some changes I made for my own purposes, mainly related to console commands. I'm putting this out here in case anyone else wants to use it, but be aware, a lot of these changes are done in a quick-and-dirty way, and there are some bugs which I may or may not fix when and if I get tired of dealing with them. That said, here are the changes I've made:

- The `loadstring` function has been added to Lua.

- I've included a script, `repl.lua`, which uses `loadstring` to provide a REPL-like interface via a `lua` console command.

    - You can access your `player_t` using the shorthand `p`. Output of previous commands can be accessed using `out[#]`, where `#` is the number shown in the output.

	- You can set something to happen repeatedly by calling `interval(#, action)`, where `#` is the interval in tics, and `action` is either a function or a string. This returns an object on which you can call `.off()` to disable it, `.on()` to re-enable it, and `.append(action)` to add an additional action to be executed immediately after.

	- You can set a watch on an expression by calling `watch(expression, label)`, where `expression` is either a function or a string, and `label` is a string to use to label the value on the screen. The `label` argument is optional, but highly recommended if you are using a function. Watches are evaluated every tic and displayed on the HUD. You can also disable a watch (removing it from the screen and preventing its evaluation) and re-enable it, in the same way as with `interval`.

	- Typing `lua_clear` in the console will reset the REPL environment, clearing all defined variables, saved outputs, intervals, and watches.

- A `sudo` console command, which does what you'd imagine it does if you're familiar with its namesake. More specifically, it temporarily sets `devparm` to true, and `multiplayer` and `netgame` to false, before executing a command, then restores their previous values afterwards. It also sets a flag which I can reference in other parts of the code to add additional command-specific overrides.

- Typing `these_arent_the_mods_youre_looking_for` in the console will clear the "game modified" command if it is set.

- Specifying the `-bypass` option for `addfile` will load the content into the game without remembering the file was added. This means the file won't be added to the WAD list, nor will the game be flagged as modified if it wasn't already. Additionally, if `-bypass` is used in a netgame, the content will simply be loaded locally. This means other clients won't load the file even if they have it, nor will they be kicked for not having it.

    - Adding files that affect gameplay is not recommended, as it will likely result in sync issues. The exception is when you're running this in the server console, and the file only makes a one-off change that can be applied to everyone with a single resync.

- Changing skin color is now possible even when you're moving, though it might cause glitches with some characters, including [my own](https://mb.srb2.org/addons/sparks-the-scarf-rider.2807/). Your skin can be changed while moving as well. As of v2.2.9, [this all works even in multiplayer](https://git.do.srb2.org/STJr/SRB2/issues/588), but doing so in a competitive game mode will generally be considered cheating, so don't do that unless you know everyone on the server is okay with it.

- You can specify `-downloadonly` on the command line if you just want to download a server's files for later. In this mode, whenever SRB2 would normally join a server (after all files are downloaded), it will instead quit to desktop. It will also bypass some checks that are only relevant to joining a server—for example, in `-downloadonly` mode, it will start downloading files from a server even if the server is currently full. This can be useful if you can't join the server at the moment but want to preload its files for when you can.

- The `-nofiles` command line parameter, commented out in vanilla SRB2, is re-enabled. This option will tell the game not to load addons when joining a server. This means you can join the server even if you don't have a file it's not willing to send, but depending on what the files are, it's likely you will crash. It can still be useful if a server has `maxsend` set too low, as you can set your name prior to joining the server, and even if you crash, they should still get the message.

- When searching for a local copy of a server addon, the game will first search in a directory called "OVERRIDE", if one exists. This folder should be in the main SRB2 folder, where `DOWNLOAD`, `config.cfg`, `gamedata.dat`, et cetera are located. If it finds a file in that location with the requested name, it will load it without verifying the MD5 hash. This can be useful if a server has an addon that does something obnoxious (e.g. `dofor` from admintools) and you want to disable that part of the addon.

- Before downloading a new file with the same name as an existing file in DOWNLOAD, the game will now rename the existing file (adding an "\_old*#*" suffix) as a backup so it doesn't get overwritten.
