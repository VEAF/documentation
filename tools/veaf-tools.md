---
description: The *veaf-tools* NodeJS application
---

Navigation: [VEAF documentation site - main page](../index.md) > [Mission Creation Tools](./index.md)

-----------------------------

🚧 **WORK IN PROGRESS** 🚧

The documentation is being reworked, piece by piece. 
In the meantime, you can browse the [old documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

# Table of Contents

- complete use case (VEAF public servers) - [here](#usecase)
- set up and use the *veaf-tools* application - [here](#installation)
- configure and use the Weather Injector - [here](veaf-tools-weather-injector.md)
- configure and use the Mission Selector - [here](veaf-tools-mission-selector.md)

# Introduction

This NodeJS application is a collection of tools that can be used to manipulate missions.

At the moment, it contains the following tools:
- Weather injector
- mission selector

# Use case

The VEAF use these tools along with some server hook lua scripts and Powershell process watchdogs (all available on [this GitHub repository][VEAF public servers repository]) to have our servers running 24/7, and restarting automatically if they crash.

Every two hours, if no one is connected (or when the last player disconnects), the server will restart.

The startup script will call the weather injector tool, which will generate a set of missions for this server, with different weather conditions (including real-world weather at the time).

Then it'll call the mission selector tool, which will select the mission to start depending on the time of the day and the schedule:

[![veaf-servers-schedule]][veaf-servers-schedule]

This ensures that our servers are always up, and set up with the mission we collectively decided to load for that time.

## Weather injector

The Weather injector is a tool that transforms a single mission file into a collection of missions, with the same content but different weather and starting conditions.

It can be used to inject a predefined DCS weather definition, read a METAR and generate a mission with the corresponding weather, or even use real-world weather.

It can also create different starting times and dates for the mission, either with absolute values (e.g., 26/01/2023 at 14:20), or with predefined "moments" (e.g., two hours after sunset).

This is a very useful tool to use with a server that runs 24/7 and that needs to have different weather conditions for each time it starts the same mission.

[Demonstration video][veaftools-injectall-demo]

## Mission selector

The mission selector is used to start a dedicated server with a specific mission, depending on a schedule that is defined in a configuration file.


# Installation

This is an autonomous tool, it does not need a specific VEAF Mission Creation Tools environment (as described [here](..\environment\index.md)).

It's therefore very easy to install on a server, or on your own computer.

***Nota bene: this chapter is also available as a [tutorial video][install-chocolatey-nodejs-veaftools]***

You'll need to install these tools on your computer:

- *NodeJS*: you need NodeJS to run the JavaScript programs in the VEAF mission creation tools; see [here](https://nodejs.org/en/)
- *yarn*: you need the Yarn package manager to fetch and update the VEAF mission creation tools; see [here](https://yarnpkg.com/)

## Install the tools using Chocolatey

The required tools can easily be installed using *Chocolatey* (see [here](https://chocolatey.org/)).

To install Chocolatey, use this command  in an elevated (admin) Powershell prompt:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

After *Chocolatey* is installed, install NodeJS by typing this simple command in a command prompt:

```cmd
choco install -y nodejs
```

Then close and reopen the command prompt.

## Install the veaf-tools application

In a command prompt type:

```cmd
npm install -g veaf-mission-creation-tools
```

Then close and reopen the command prompt.

# General usage of the application

To run the VEAF tools, simply type `veaf-tools` in a command prompt.

[![veaftools-options]][veaftools-options]

# Updating the application

To update the VEAF tools, simply do the same as for the installation.

In a command prompt type:

```cmd
npm install -g veaf-mission-creation-tools
```

NPM will fetch and install the latest version automatically.
# Using the Weather injector

Please consult the [Weather injector documentation](./veaf-tools-weather-injector.md) for more information.

# Using the Mission selector

Please consult the [Mission selector documentation](./veaf-tools-mission-selector.md) for more information.

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

[install-chocolatey-nodejs-veaftools]: ../images/install-chocolatey-nodejs-veaftools.mp4
[veaftools-options]: ../images/veaftools-options.png
[veaf-servers-schedule]: ../images/veaf-servers-schedule.png
[veaftools-injectall-demo]: ../images/veaftools-injectall-demo.mp4