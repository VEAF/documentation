---
description: Injecteur de plans de vols
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

L'outil *Injecteur de plans de vols* permet de gérer de manière facile et centralisée les plans de vol (waypoints) des différents appareils de
 vos missions.

Ainsi, vous pourrez facilement ajouter ou modifier un point partagé par tous les vols, ou ajouter un nouveau vol sans avoir besoin de lui assigner tous les points partagés. En particulier, c'est très utile pour le bullseye, et autres points de report centralisés.

Grâce à cet outil, il devient très aisé de maintenir un ensemble de points de report commun pour chaque coalition de votre mission, et de le copier d'une mission à l'autre : si vous commencez une nouvelle mission sur Caucase, il suffit de reprendre le fichier de configuration de votre précédente mission sur Caucase, et hop tous les avions ont les bons waypoints !

# Principes

Le fonctionnement de l'outil est très simple : il se lance automatiquement au *build* de la mission (voir la description du  [cycle de production](./index.md#cycle-de-production)), lit le fichier de configuration qui lui est dédié (`src/waypoints/waypointsSettings.lua`) et met à jour les groupes DCS de votre mission en conséquence.

# Comment configurer l'outil

Tout se trouve dans le fichier de configuration `waypointsSettings.lua`, qui est situé dans le répertoire `src/waypoints` de votre mission.

Ce dernier est un fragment de code source [LUA](https://fr.wikipedia.org/wiki/Lua) qui permet de choisir la manière dont les scripts VEAF sont activés et configurés. Nous vous conseillons d'utiliser Notepad++ ou Visual Studio Code pour l'éditer.

Par défaut, si vous avez utilisé le [convertisseur de mission existante][VEAF-mission-converter-repository] pour préparer votre dossier de mission VEAF, il contient déjà la configuration nécessaire.

Vous pouvez aussi copier le fichier que nous utilisons pour nos propres missions VEAF:

- [Caucase](https://github.com/VEAF/VEAF-Open-Training-Mission-Caucasus/blob/master/src/waypoints/waypointsSettings.lua)
- [Marianes](https://github.com/VEAF/VEAF-Open-Training-Mission-Marianas/blob/master/src/waypoints/waypointsSettings.lua)
- [Syrie](https://github.com/VEAF/VEAF-Open-Training-Mission-Syria/blob/master/src/waypoints/waypointsSettings.lua)
- [Sinai](https://github.com/VEAF/VEAF-Open-Training-Mission-Sinai/blob/master/src/waypoints/waypointsSettings.lua)
- [Channel](https://github.com/VEAF/VEAF-Open-Training-WW2-TheChannel/blob/main/src/waypoints/waypointsSettings.lua)

## Syntaxe du fichier de configuration

Ce fichier est un dictionnaire LUA, composé de deux parties:

- la définition des points de report
- l'assignation de ces points à des groupes DCS

Dans la première partie, on définit les points de report pour toute la mission.

Exemple:
```lua
waypoints =
{
    ["BULLSEYE_BLUE"] = {
        ["type"] = "Turning Point",
        ["action"] = "Turning Point",
        ["alt"] = 6096, -- 20000 ft
        ["alt_type"] = "BARO",
        ["ETA"] = 364.89432745775,
        ["ETA_locked"] = false,
        ["speed"] = 999,
        ["speed_locked"] = true,
        ["name"] = "BULLSEYE",
        ["x"] = 75869,
        ["y"] = 48674,
    }, -- end of [BULLSEYE]
}
```

Quand on veut faire référence à une de ces points, il faut employer le nom qui sert à le référencer (dans l'exemple, `BULLSEYE_BLUE`) et non pas le nom du point dans DCS (dans l'exemple, `BULLSEYE`).

Dans la seconde partie, on assigne les waypoints aux différents groupes.

Pour chaque groupe d'appareil qu'on souhaite configurer, on ajoutera une section, à laquelle on donne un nom, un ensemble de paramètres qui définissent le(s) groupe(s) sélectionnés, et un extrait de LUA qui sera recopié dans la définition des groupes par l'outil.

La syntaxe est la même que pour [l'injecteur de presets radio](./presets.md).

Exemple:

```lua
    ["all blue planes"] =
    {
        category = "plane",
        coalition = "blue",
        type = nil,
        country = nil,

        ["waypoints"] =
        {
            ["BULLSEYE_BLUE"] = "BULLSEYE_BLUE",
            ["SENAKI_AB"] = "SENAKI_AB"
        }
    }
```

Ici, on crée une définition qu'on nomme *all blue planes*.

Ses paramètres sont de sélectionner tous les groupes de la coalition bleue (`coalition = "blue"`), quel qu'en soit le pays (`country = nil`), et le type d'appareil.

Ensuite, le bloc `["Radio"]` est recopié dans la mission, non sans avoir remplacé toutes les références aux canaux des plans de fréquences par leur valeur; par exemple, `[1]  = radioPresetsBlue["##RADIO1_01##"]` sera remplacé par `[1]  = 243.000` pour peu qu'on ait défini ça dans le plan de fréquence *radioPresetsBlue*.

Pour plus de facilité, dans le cas où on veut sélectionner plusieurs types d'appareils qui ont un nom proche (c'est souvent le cas dans les variantes comme le *Mirage F1 CE* et le *Mirage F1 BE* par exemple), on peut utiliser le sélecteur `typePattern` en lieu et place de `type`. Dans ce cas, on devra utiliser une [pattern LUA](http://wxlua.free.fr/Tutoriel_Lua/Tuto/Strings/strings6.php), ce qui peut être un peu compliqué.

Voici un exemple: 
```lua
    ["blue Mirage-F1s"] = {
        typePattern = "Mirage[-]F1.*",
        coalition = "blue",
        country = nil,
        ...
    }
```

Notez deux choses:

- on utilise `.*` pour dire "rien du tout ou autant de caractères qu'on veut, quels qu'ils soient"; ici ça pourra "matcher" *CE*, *BE*, ou même *Choucroute* si on a un Mirage-F1Choucroute un jour.
- on doit utiliser `[-]` pour dire `-` parce que le tiret est un caractère spécial.

N'hésitez pas à me contacter si vous avez des questions !

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
