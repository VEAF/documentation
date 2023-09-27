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

*Skynet-IADS* change complétement la manière dont fonctionnent les défenses antiaériennes dans DCS. Il est ainsi quasi indispensable à toute mission qui comporte des radars au sol. Malheureusement, sa configuration nécéssite de mettre en place et de maintenir un script dans chaque mission, script dont la complexité augmente avec la taille de la mission. Les modules VEAF documentés ici automatisent la construction de ce script et offrent des outils pour consulter et vérifier les réseaux ainsi créés.

# SkynetIadsHelper

Ce module construit automatiquement des réseaux IADS Skynet à partir des groupes présents dans la mission.  
Pour cela il va parcourir la liste de tous les groupes de la mission, et ajouter ceux qui sont éligibles dans les réseaux IADS Skynet. Cette initialisation des réseaux se fait au démarrage de la mission (avec un délai paramétrable) sur la base des groupes qui existent à ce moment. Les groupes en activation retardée sont intégrés, mais pas les groupes qui seront générés plus tard par script.

## Utilisation

Le module est activé par défaut dans les templates de mission VEAF.  
L'initialisation se fait sur le modèle suivant :

```lua
if (veafSkynet) then
    veafSkynet.PointDefenceMode = veafSkynet.PointDefenceModes.Skynet
    veafSkynet.GroupIntegrationMode = veafSkynet.Strict
    veafSkynet.DynamicSpawn = false
    veafSkynet.DelayForStartup = 5
    veafSkynet.initialize(
        false, --includeRedInRadio=true
        false, --debugRed
        false, --includeBlueInRadio
        false --debugBlue
    )
end
```

**PointDefenceMode**  
Le module peut identifier les sites SAM capables d'intercepter des missiles antiradar, et essayer des les affecter en défense rapprochée d'autres élements du réseau à proximité.  
La propriété générale `veafSkynet.PointDefenceMode` pilote cette mécanique. Elle est à positionner avant le `initialize`. Les valeurs possibles sont décrites dans l'énumeration `veafSkynet.PointDefenceModes` :
- **`None = 0`** : le module n'essayera pas de créer des Point Defences (**valeur par défaut**)
- `Skynet = 1` : le module configure les Point Defences avec les mécaniques du script Skynet
- `Dcs = 2` : le module exclue les Point Defences du réseau IADS Skynet et les laisse aux bons soins de l'IA de DCS (toujours allumés)

Si la création de défenses rapprochée est souhaitée, le mode `Skynet` devrait être l'option à retenir. Le mode `Dcs` donne la possibilité d'avoir des défenses potentiellement plus efficaces mais aussi plus vulnérables (car toujours allumées).

**GroupIntegrationMode**  
La propriété générale `veafSkynet.GroupIntegrationMode` permet de choisir les critères que le module utilise pour décider si un groupe DCS doit être intégré dans les réseaux Skynet. Elle est à positionner avant le `initialize`. Les valeurs possibles sont décrites dans l'énumeration `veafSkynet.GroupIntegrationModes` :
- `Strict = 0` : seuls les groupes DCS composés uniquement d'unités connues de Skynet sont intégrés dans les réseaux IADS
- **`Lenient = 1`** : seuls les groupes DCS composés de au moins une unité connue de Skynet sont intégrés dans les réseaux IADS (**valeur par défaut**)

Cette propriété a un impact sur l'intégration des groupes "mixtes" dans les réseau. Typiquement, un convoi composé de tanks et transports, escorté par des SA-19, sera intégré en `Lenient` mais pas en `Strict`.

*Un groupe connu de Skynet est en règle générale un radar, un lanceur, ou une unité antiaérienne autonome (AAA, SA-15...)*

**DynamicSpawn**  
Dynamic Spawn, interactions VeafSpawn

**DelayForStartup**

**Command Centers**  
`veafSkynet.addCommandCenterOfCoalition(iCoalitionId, sCommandCenterName)`
`veafSkynet.destroyCommandCentersOfCoalition(iCoalitionId, iExplosionStrength)`

**Accéder aux réseaux IADS générés**

# SkynetIadsMonitor

Le module *SkynetIadsMonitor* offre des outils statiques et dynamiques pour surveiller l'état et la composition des réseaux IADS Skynet. Il permet principalement de construire des descriptifs textuels des réseaux Skynet, afin de vérifier leurs structures.

*A compléter*

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
