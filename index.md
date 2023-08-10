---
title: VEAF tools and scripts documentation site
description: VEAF tools and scripts documentation for mission makers, users and programmers
---

Navigation: you're on the main page

-----------------------------

🚧 **WORK IN PROGRESS** 🚧

The documentation is being reworked, piece by piece. 
In the meantime, you can browse the [old documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

# Table of Contents

- set up and use the VEAF tools (*for all*) - [here](./tools/index.md)
- setup a VEAF mission-maker environment (*for mission makers*) - [here](./environment/index.md)
- discover the VEAF mission-maker features (*for mission makers*) - [here](./mission-maker/index.md)
- learn how to program the scripts in Lua (*for programmers*) - [here](./programmer/index.md)

# Introduction

The VEAF Mission Creation Tools provides tools and scripts designed to easily create, share and maintain dynamic missions.

They regroup

* tools to manipulate DCS mission files and servers
* the VEAF mission scripts (organized in modules)
* the VEAF server hooks
* some of the community scripts, sometimes edited by VEAF (e.g., CTLD, MiST)
* an easy mission creation, edition and publication workflow
* tools to support this workflow, including a converter that build a dynamic mission from an existing static one
* this documentation

Our GitHub repositories:

* the [main][VEAF-Mission-Creation-Tools-repository] repository contains all the sources and the documentation base
* the [mission converter][VEAF-mission-converter-repository] can be forked or downloaded to inject the scripts and tools into an existing mission
* the [demo mission][VEAF-demo-mission-repository] (again, fork or download) is a small, simple mission that uses some of the latest features of the tools
* the VEAF [Caucasus training mission][VEAF-Open-Training-Mission-repository] (fork or download) is a good working example of the scripts in a complex mission
* the VEAF [multiplayer mission repository][VEAF-Multiplayer-Missions-repository] contains mission that we played with the VEAF (some may be old and deprecated!)

# What do I need to start?

You'll need to set up an environment, on your PC, with specific (free) software.

Read this [page](./environment/index.md) for more information.

# What tools are there and how do I use them?

The VEAF Mission Creation Tools provides a lot of tools and scripts.

Most of them are meant to be used in the mission build pipeline (i.e., by a mission maker working on a mission, read the [mission maker documentation](./mission-maker/index.md)), but some can be used as standalone tools:
- the [LUA dictionary normalizer](./tools/lua_dictionary_normalizer.md) which makes comparing LUA files easier
- the [Weather Injector](./tools/veaf-tools-weather-injector.md) which can generate multiple mission files with different starting times and weather conditions from a template
- the [Mission Selector](./tools/veaf-tools-mission-selector.md) which selects a starting mission for your dedicated server from a list of missions and a schedule.

# How do I use the VEAF scripts in a mission I want to design?

Please read the [mission maker documentation](./mission-maker/index.md).

For those looking for a quick start, fork or download the [mission converter](https://github.com/VEAF/VEAF-mission-converter) and follow the instructions of the `readme.md` file. You'll learn how to can use the VEAF Mission Creation Tools in your own existing mission.

You can also fork or download the [demo mission](https://github.com/VEAF/VEAF-Demo-Mission) to see what can be done (usually only the latest features are demonstrated here), and the [VEAF Caucasus training mission](https://github.com/VEAF/VEAF-Open-Training-Mission) which is a very complex, open and dynamic training mission that uses a lot of the features.

# How do I contribute to this wonderful repository?

First, thank you!

We always welcome help and new ideas.

Please always use branches and pull requests! Start with forking the VEAF-Mission-Creation-Tools [repository](https://github.com/VEAF/VEAF-Mission-Creation-Tools), create a branch, hack away and publish your work.

# I want to help maintain the documentation!

The easiest way of doing this is by editing the files directly on the Github website.

But you can also fork the [main][VEAF-Mission-Creation-Tools-repository] repository.

# I need to add new features or correct bugs in the scripts!

Please read the [programmer documentation](./programmer/index.md).

# Contacts

-----------------------------

Made and maintained by the Virtual European Air Force, a French DCS pilot community.

[![VEAF-logo]][VEAF website]
[![Badge-Discord]][VEAF Discord]

-----------------------------

If you need help or you want to suggest something, you can

* contact **Zip** on [GitHub][Zip on Github] or on [Discord][Zip on Discord]
* go to the [VEAF website]
* post on the [VEAF forum]
* join the [VEAF Discord]


[Badge-Discord]: https://img.shields.io/discord/471061487662792715?label=VEAF%20Discord&style=for-the-badge
[VEAF-logo]: ./images/logo.png


[VEAF Discord]: https://www.veaf.org/discord
[Zip on Github]: https://github.com/davidp57
[Zip on Discord]: https://discordapp.com/users/421317390807203850
[VEAF website]: https://www.veaf.org
[VEAF forum]: https://www.veaf.org/forum

[VEAF-Mission-Creation-Tools-repository]: https://github.com/VEAF/VEAF-Mission-Creation-Tools
[VEAF-mission-converter-repository]:https://github.com/VEAF/VEAF-mission-converter
[VEAF-demo-mission-repository]: https://github.com/VEAF/VEAF-Demo-Mission
[VEAF-Open-Training-Mission-repository]:https://github.com/VEAF/VEAF-Open-Training-Mission
[VEAF-Multiplayer-Missions-repository]: https://github.com/VEAF/VEAF-Multiplayer-Missions
