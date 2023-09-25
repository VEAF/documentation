---
description: Automatisation et surveillance des réseau IADS gérés par Skynet
---

-----------------------------

Navigation: [page "Mission Maker" du site de documentation VEAF](./index.md)

-----------------------------

🚧 **TRAVAUX EN COURS** 🚧

La documentation est en train d'être retravaillée, morceau par morceau. 
En attendant, vous pouvez consulter l'[ancienne documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

# Table des matières

- Principes - [ici](#principes)
- Configuration - [ici](#comment-configurer-le-module)
- Exemples - [ici](#exemples-complets)

# Introduction

Le module *SkynetIadsHelper* construit automatiquement des réseaux IADS Skynet à partir des groupes présents dans la mission.  
Le module *SkynetIadsMonitor* offre des outils statiques et dynamiques pour surveiller l'état et la composition des réseaux IADS Skynet.

# Principes

[Synet-IADS](https://github.com/walder/Skynet-IADS) est un script qui pilote les systèmes radar antiaériens afin qu'ils optimisent leur survivabilité et leur léthalité en restant éteints le plus possible. Il simule ainsi un IADS (Integrated Air Defence System) dans lequel les EWR (Early Warning Radar) scannent le ciel pour repérer et surveiller des contacts, et communiquent ces informations aux sites SAM (Surface-to-Air Missile), permettant à ceux-ci de ne s'activer que lorsqu'ils sont en capacité d'engager un contact.

*Skynet-IADS* change complétement la manière dont fonctionnent les défenses antiaéreinnes dans DCS. Il est ainsi quasi indispensable à toute mission qui comporte des radars au sol. Malheureusement, sa configuration nécéssite de mettre en place et de maintenir un script dans chaque mission, script dont la complexité augmente avec la taille de la mission. Les modules VEAF documentés ici automatisent la construction de ce script et offrent des outils pour consulter et vérifier les réseaux ainsi créés.

# SkynetIadsHelper

Le module *SkynetIadsHelper* construit automatiquement des réseaux IADS Skynet à partir des groupes présents dans la mission.

if (veafSkynet) then
    veafSkynet.PointDefenceMode = veafSkynet.PointDefenceModes.Skynet
    veafSkynet.GroupIntegrationMode = veafSkynet.Strict
    veafSkynet.addCommandCenterOfCoalition(coalition.side.RED, "CommandCenterRed")
    veafSkynet.DelayForStartup = 5
    veaf.loggers.get(veaf.Id):info("init - veafSkynet")
    veafSkynet.initialize(
        false, --includeRedInRadio=true
        false, --debugRed
        false, --includeBlueInRadio
        false --debugBlue
    )
end

Concept général, mode strict/lenient

Point defences

Dynamic Spawn, interactions VeafSpawn

Fin d'initialisation, récupéation des réseaux créés

Command Centers




# SkynetIadsMonitor

Le module *SkynetIadsMonitor* offre des outils statiques et dynamiques pour surveiller l'état et la composition des réseaux IADS Skynet.

# Contacts

Si vous avez besoin d'aide, ou si vous voulez suggérer quelque chose, vous pouvez :

* contacter **Zip** sur [GitHub][Zip on Github] ou sur [Discord][Zip on Discord]
* aller consulter le [site de la VEAF][VEAF website]
* poster sur le [forum de la VEAF][VEAF forum]
* rejoindre le [Discord de la VEAF][VEAF Discord]


[Badge-Discord]: https://img.shields.io/discord/471061487662792715?label=VEAF%20Discord&style=for-the-badge
[VEAF-logo]: ../images/logo.png?raw=true
[VEAF Discord]: https://www.veaf.org/discord
[Zip on Github]: https://github.com/davidp57
[Zip on Discord]: https://discordapp.com/users/421317390807203850
[VEAF website]: https://www.veaf.org
[VEAF forum]: https://www.veaf.org/forum

[VEAF-Mission-Creation-Tools-repository]: https://github.com/VEAF/VEAF-Mission-Creation-Tools
[VEAF-mission-converter-repository]:https://github.com/VEAF/VEAF-mission-converter
[VEAF-demo-mission-repository]: https://github.com/VEAF/VEAF-Demo-Mission
[VEAF-Open-Training-Mission-repository]: https://github.com/VEAF/VEAF-Open-Training-Mission
[VEAF-Multiplayer-Missions-repository]: https://github.com/VEAF/VEAF-Multiplayer-Missions
[VEAF-Open-Training-Mission-documentation]: https://www.veaf.org/opentraining

[veaf_radio_menu]: ../images/radio_veaf_menu.png
[user_radio_menu]: ../images/radio_user_menu.png
