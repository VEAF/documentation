---
description: Tools for mission makers and server admins
---

Navigation: [VEAF documentation site - main page](../index.md)

-----------------------------

🚧 **WORK IN PROGRESS** 🚧

The documentation is being reworked, piece by piece. 
In the meantime, you can browse the [old documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

# Table of Contents

- setup and use the *veaf-tools* application - [here](veaf-tools.md)
- configure and use the Weather Injector - [here](veaf-tools-weather-injector.md)
- configure and use the Mission Selector - [here](veaf-tools-mission-selector.md)
- configure and use the LUA dictionary normalizer - [here](lua_dictionary_normalizer.md)

# Introduction

The VEAF Mission Creation Tools provides scripts that make missions behave dynamically, and tools that are used to manipulate missions.

# *veaf-tools* application

This NodeJS application is a collection of tools that can be used to manipulate missions.

At the moment, it contains the following tools:
- Weather injector
- mission selector

See the [VEAF Tools application documentation page](veaf-tools.md) for more details.

## Weather injector

The Weather injector is a tool that transforms a single mission file into a collection of missions, with the same content but different weather and starting conditions.

It can be used to inject a predefined DCS weather definition, read a METAR and generate a mission with the corresponding weather, or even use real-world weather.

It can also create different starting times and dates for the mission, either with absolute values (e.g., 26/01/2023 at 2:20 p.m.), or with predefined "moments" (e.g., two hours after sunset).

This is a very useful tool to use with a server that runs 24/7 and that needs to have different weather conditions every time it starts the same mission.

See the [VEAF Tools application documentation page](veaf-tools.md) for more details.

## Mission selector

The mission selector is used to start a dedicated server with a specific mission, depending on a schedule that is defined in a configuration file.

See the [VEAF Tools application documentation page](veaf-tools.md) for more details.

# LUA dictionary normalizer

TBD

Full documentation is available [here](lua_dictionary_normalizer.md).

# Contacts

-----------------------------

Made and maintained by the Virtual European Air Force, a French DCS pilot community.

[![VEAF-logo]][VEAF website]
[![Badge-Discord]][VEAF Discord]

-----------------------------

If you need help or you want to suggest something, you can

* contact [Zip][Zip on Github] on GitHub
* go to the [VEAF website]
* post on the [VEAF forum]
* join the [VEAF Discord]


[Badge-Discord]: https://img.shields.io/discord/471061487662792715?label=VEAF%20Discord&style=for-the-badge
[VEAF-logo]: ../images/logo.png


[VEAF Discord]: https://www.veaf.org/discord
[Zip on Github]: https://github.com/davidp57
[VEAF website]: https://www.veaf.org
[VEAF forum]: https://www.veaf.org/forum

[demo-mission-structure]: ../images/demo-mission-structure.png
[workflow-01]: ../images/editor_workflow.png
