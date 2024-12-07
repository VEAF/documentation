---
description: Gestion de la météo
---

-----------------------------

Navigation: [page "Mission Maker" du site de documentation VEAF](./index.md)

-----------------------------

🚧 **TRAVAUX EN COURS** 🚧

La documentation est en train d'être retravaillée, morceau par morceau. 
En attendant, vous pouvez consulter l'[ancienne documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

# Table des matières

- Introduction - [ici](#introduction)
- Principes et fonctionnement - [ici](#utilisation)
- Mise en place - [ici](#mise-en-place)
- Configuration dans MissionConfig ou dans des triggers [ici](#configuration)

## Introduction

Ce module a pour but de permettre la gestion de la météo; il offre:

- la possibilité de gérer le brouillard: 
  - statique
  - animé (s'établit ou se dissipe sur une période donnée)
  - dynamique (est géré par le système en fonction des conditions météo, de l'heure et de l'emplacement)
- l'obtention d'informations météo via le menu radio
- l'exploitation d'informations de type ATIS, concernant la base aérienne la plus proche (piste en service, etc.)

(Veuillez lire [Mission Maker](./index.md)).

## Utilisation

Le menu radio "WEATHER AND ATC" permet d'obtenir des informations détaillées sur la météo et le fonctionnement de la base aérienne la plus proche. C'est dans ce menu également que vous pourrez gérer le brouillard.

### Informations météo et ATC

En fonction des données présentes dans la mission, ainsi que de l'unité qui demande l'information (type d'appareil par exemple), on reçoit des messages concernant la météo et l'ATIS avec des unités et des détails conformes à notre appareil.

On peut demander la météo au plus proche de notre position, avec la commande "Weather on closest point".

Il est aussi possible d'obtenir les informations ATIS de la base aérienne la plus proche, en utilisant la commande "ATC on closest airbase". Ces données sont toutes deux complémentaires, en particulier :

- "Weather" est taillé pour votre avion; les données fournies et les unités employées lui correspondent
- "ATC" contient des informations concernant la piste en service

Il est également possible d'utiliser la commande "ATC and weather in one go" pour obtenir les deux messages d'une seule fois.

Enfin, si vous avez installé un client de notre interface d'accès à distance (script `veafRemote.lua`) comme le hook serveur que nous proposons, vous pouvez utiliser le chat de votre session multijoueur ("chat to all") pour envoyer la commande `atc` pour obtenir les mêmes informations.

### Gestion du brouillard

Le brouillard peut être géré par le menu radio également.

Plusieurs types d'effet de brouillard peuvent être activés:

- statique: le brouillard décrit prend effet immédiatement
- animé: le brouillard décrit prend effet sur une période donnée, progressivement
- dynamique: le brouillard décrit se comporte naturellement en fonction des paramètres météo (point de rosée, température, vent), de la saison et de l'heure de la journée

Avec un client de l'interface à distance installé, vous pouvez utiliser le chat de votre session multijoueur ("chat to all") pour envoyer la commande `weather fog` suivie du nom de l'effet de brouillard (voir la liste des effets au chapitre [configuration](#configuration)) pour l'activer.

Exemple: `weather fog FOG_DYNAMIC_MEDIUM` (ou en minuscules `weather fog fog_dynamic_medium` c'est pareil)

## Mise en place

Les informations météo sont stockées dans la mission.

Ce sont ces paramètres qui vous seront présentés dans les messages d'information.

### Météo dans l'éditeur DCS

Les paramètres de la météo sont gérés par DCS via le menu "Weather" de l'éditeur de mission.

### Injecteur météo

Il est également possible d'utiliser [l'injecteur de conditions météo](../tools/veaf-tools-weather-injector.md) pour mettre en place une météo.

## Configuration

Dans le fichier *missionConfig.lua*, il faut activer le module *veafWeather* et l'initialiser:

```lua
if veafWeather then
    veaf.loggers.get(veaf.Id):info("init - veafWeather")
    veafWeather.initialize()
end
```

En plus de gérer le brouillard via le menu radio (voir [utilisation](#utilisation)), le mission maker peut utiliser des commandes LUA dans son code (trigger, ou *missionConfig.lua*).

Il suffit de référer une des constantes prédéfinies qui décrivent un effet de brouillard, et de l'activer; par exemple:

```lua
veafWeather.FOG_DYNAMIC_MEDIUM:activate()
```

Les constantes sont décrites ci-dessous.

Instances gérées dynamiquement:

```lua
veafWeather.FOG_DYNAMIC_HEAVY
veafWeather.FOG_DYNAMIC_MEDIUM
veafWeather.FOG_DYNAMIC_SPARSE
```

Instances gérées dynamiquement:

```lua
veafWeather.FOG_STATIC_MEDIUM
veafWeather.FOG_STATIC_MEDIUM_LOW
veafWeather.FOG_STATIC_SPARSE
veafWeather.FOG_STATIC_SPARSE_LOW
veafWeather.FOG_STATIC_NO
```

Instances animées; disponibles en version 1, 5, 10, 15, 30, 60, and 90 minutes; les exemples ci-dessous sont pour 5 minutes, remplacer le nombre de minutes à volonté:

```lua
veafWeather.FOG_ANIMATED_5M_HEAVY
veafWeather.FOG_ANIMATED_5M_MEDIUM
veafWeather.FOG_ANIMATED_5M_MEDIUM_LOW
veafWeather.FOG_ANIMATED_5M_SPARSE
veafWeather.FOG_ANIMATED_5M_SPARSE_LOW
veafWeather.FOG_ANIMATED_5M_NO
```

## Contacts

Si vous avez besoin d'aide, ou si vous voulez suggérer quelque chose, vous pouvez :

* contacter **Rex** sur [GitHub][Rex on Github] ou sur [Discord][Rex on Discord]
* contacter **Zip** sur [GitHub][Zip on Github] ou sur [Discord][Zip on Discord]
* aller consulter le [site de la VEAF][VEAF website]
* poster sur le [forum de la VEAF][VEAF forum]
* rejoindre le [Discord de la VEAF][VEAF Discord]


[Badge-Discord]: https://img.shields.io/discord/471061487662792715?label=VEAF%20Discord&style=for-the-badge
[VEAF-logo]: ../images/logo.png?raw=true
[VEAF Discord]: https://www.veaf.org/discord
[Zip on Github]: https://github.com/davidp57
[Zip on Discord]: https://discordapp.com/users/421317390807203850
[Rex on Github]: https://github.com/RexAttaque
[Rex on Discord]: https://discordapp.com/users/256509086777081856
[VEAF website]: https://www.veaf.org
[VEAF forum]: https://www.veaf.org/forum

[VEAF-Mission-Creation-Tools-repository]: https://github.com/VEAF/VEAF-Mission-Creation-Tools
[VEAF-mission-converter-repository]:https://github.com/VEAF/VEAF-mission-converter
[VEAF-demo-mission-repository]: https://github.com/VEAF/VEAF-Demo-Mission
[VEAF-Open-Training-Mission-repository]: https://github.com/VEAF/VEAF-Open-Training-Mission
[VEAF-Multiplayer-Missions-repository]: https://github.com/VEAF/VEAF-Multiplayer-Missions

[demo-mission-structure]: ../images/demo-mission-structure.png
