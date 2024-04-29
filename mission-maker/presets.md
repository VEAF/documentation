---
description: Injecteur de presets radio
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

L'outil *Injecteur de presets radio* permet de gérer de manière facile et centralisée les presets radio (canaux pré-réglés) des différents appareils de vos missions.

Ainsi, vous avez un seul endroit où changer une fréquence pour un canal, et tous les appareils sont mis à jour automatiquement; pas de risque d'oublier de changer la fréquence pour un ou l'autre appareil parmi les 500 de votre mission.

Grâce à cet outil, il devient très aisé de maintenir un plan de fréquence commun pour chaque coalition de votre mission, et de le copier d'une mission à l'autre : si vous commencez une nouvelle mission sur Caucase, il suffit de reprendre le fichier de configuration de votre précédente mission sur Caucase, et hop tous les avions ont les bons presets !

# Principes

Le fonctionnement de l'outil est très simple : il se lance automatiquement au *build* de la mission (voir la description du  [cycle de production](./index.md#cycle-de-production)), lit le fichier de configuration qui lui est dédié (`src/radio/radioSettings.lua`) et met à jour les groupes DCS de votre mission en conséquence.

# Comment configurer l'outil

Tout se trouve dans le fichier de configuration `radioSettings.lua`, qui est situé dans le répertoire `src/radio` de votre mission.

Ce dernier est un fragment de code source [LUA](https://fr.wikipedia.org/wiki/Lua) qui permet de choisir la manière dont les scripts VEAF sont activés et configurés. Nous vous conseillons d'utiliser Notepad++ ou Visual Studio Code pour l'éditer.

Par défaut, si vous avez utilisé le [convertisseur de mission existante][VEAF-mission-converter-repository] pour préparer votre dossier de mission VEAF, il contient déjà la configuration nécessaire.

Vous pouvez aussi copier le fichier que nous utilisons pour nos propres missions VEAF:

- [Caucase](https://github.com/VEAF/VEAF-Open-Training-Mission-Caucasus/blob/master/src/radio/radioSettings.lua)
- [Marianes](https://github.com/VEAF/VEAF-Open-Training-Mission-Marianas/blob/master/src/radio/radioSettings.lua)
- [Syrie](https://github.com/VEAF/VEAF-Open-Training-Mission-Syria/blob/master/src/radio/radioSettings.lua)
- [Sinai](https://github.com/VEAF/VEAF-Open-Training-Mission-Sinai/blob/master/src/radio/radioSettings.lua)
- [Channel](https://github.com/VEAF/VEAF-Open-Training-WW2-TheChannel/blob/main/src/radio/radioSettings.lua)

## Syntaxe du fichier de configuration

Ce fichier est un dictionnaire LUA, composé de deux parties:

- la définition des canaux
- l'assignation de canaux à des groupes DCS

Dans la première partie, on définit les fréquences correspondant à des ensembles de canaux. Par exemple, on va définir les canaux pour les aéronefs modernes des deux coalitions bleue et rouge.

On pourra également définir ici les canaux d'appareils moins conventionnels, comme des warbirds, ou des avions de la guerre froide.

Exemple:
```lua
  radioPresetsBlue =
  {
      -- radio 1 : left radio, red radio, UHF radio - channel frequency (Default range is 225MHz to 390MHz)
      ["##RADIO1_01##"] = 243.000,
      ["##RADIO1_02##"] = 260.000,
      ...
      ["##RADIO1_19##"] = 290.400,
      ["##RADIO1_20##"] = 290.500,

      -- radio 1 : left radio, red radio, UHF radio - channel names
      ["##RADIO1_NAME_01##"] = "Guard",
      ["##RADIO1_NAME_02##"] = "Batumi / 16X",
      ...
      ["##RADIO1_NAME_19##"] = "Shell-2/BM/63Y",
      ["##RADIO1_NAME_20##"] = "Arco-1/BM/64Y",

      -- radio 2 : right radio, green radio, VHF radio - channel frequency  (Default range is 118MHz to 150MHz)
      ["##RADIO2_01##"] = 121.500,
      ["##RADIO2_02##"] = 120.000,
      ...
  }
```

Quand on veut faire référence à une de ces fréquences, il faut employer le nom du plan de fréquence, et le nom du canal ; par exemple `radioPresetsBlue["##RADIO1_01##"]`.

Dans la seconde partie, on définit les presets radio des différentes groupes; bien entendu, on peut référencer les fréquences qu'on a définies dans la première partie (c'est même conseillé). De cette manière, en cas de changement de plan de fréquence (par exemple, on décide que le canal 14 sera 282.10 au lieu de 282.20), il suffira de changer à un seul endroit cette fréquence.

Pour chaque groupe d'appareil qu'on souhaite configurer, on ajoutera une section, à laquelle on donne un nom, un ensemble de paramètres qui définissent le(s) groupe(s) sélectionnés, et un extrait de LUA qui sera recopié dans la définition des groupes par l'outil.

Exemple:

```lua
    ["blue FA-18C_hornet"] = {
        type = "FA-18C_hornet",
        coalition = "blue",
        country = nil,

        ["Radio"] =
        {
            --V/UHF (down to 30MHz) with modulation selection box
            [1] =
            {
                ["modulations"] =
                {
                    [1] =  0,
                    [2] =  0,
                    [3] =  0,
                    ...
                    [19] = 0,
                    [20] = 0,
                }, -- end of ["modulations"]
                ["channels"] =
                {
                    [1]  = radioPresetsBlue["##RADIO1_01##"],
                    [2]  = radioPresetsBlue["##RADIO1_02##"],
                    [3]  = radioPresetsBlue["##RADIO1_03##"],
                    ...
                    [19] = radioPresetsBlue["##RADIO1_19##"],
                    [20] = radioPresetsBlue["##RADIO1_20##"],
                }, -- end of ["channels"]
            }, -- end of [1]
            ...
        }
    }
```

Ici, on crée une définition qu'on nomme *blue FA-18C_hornet*.

Ses paramètres sont de sélectionner tous les groupes de la coalition bleue (`coalition = "blue"`), quel qu'en soit le pays (`country = nil`), dont le type d'appareil est précisément *FA-18C_hornet* (`type = "FA-18C_hornet"`).

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
