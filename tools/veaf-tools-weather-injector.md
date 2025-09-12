---
description: Permet de générer des versions d'une mission à différents moments de la journée et avec différentes météos
---

# Injecteur de Météo et de Moments

Navigation: [Site de documentation VEAF - page principale](../index.md) > [Outils de Création de Mission](./index.md) > [Application *veaf-tools*](./veaf-tools.md)

-----------------------------

🚧 **TRAVAUX EN COURS** 🚧

La documentation est en cours de révision, partie par partie.
En attendant, vous pouvez consulter l'[ancienne documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

## Table des matières

- utiliser l'Injecteur Météo - [voir ici](#utiliser-linjecteur-météo)
- créer le fichier de versions - [voir ici](#fichier-de-versions)
- configurer le fichier de configuration - [voir ici](#fichier-de-configuration)
- exemple de météo réelle - [voir ici](#injection-de-météo-réelle)
- exemple de météo prédéfinie - [voir ici](#injection-dune-météo-prédéfinie)

## Introduction

L'Injecteur Météo fait partie de l'application VEAF Tools. Consultez l'installation et la description dans la [documentation générale de l'application VEAF Tools](./veaf-tools.md).

## Utiliser l'Injecteur Météo

L'Injecteur Météo est en fait composé de deux commandes de l'application *veaf-tools*.

La commande `inject` injectera la météo dans le fichier de mission que vous spécifiez, et créera un nouveau fichier de mission avec la météo et les conditions de départ que vous avez spécifiées dans les options de ligne de commande.

Tapez `veaf-tools inject --help` pour obtenir de l'aide :

[![veaftools-inject-options]][veaftools-inject-options]

La commande `injectall` lira un fichier de versions contenant plusieurs conditions météorologiques et de départ, et les injectera dans le fichier de mission source, créant une collection de fichiers de mission cibles.

Tapez `veaf-tools injectall --help` pour obtenir de l'aide :

[![veaftools-injectall-options]][veaftools-injectall-options]

### Options

#### Options obligatoires en ligne de commande

Les options suivantes sont obligatoires pour les commandes `inject` et `injectall` ; n'utilisez pas le nom de l'option, ce sont des arguments positionnels (c'est-à-dire que vous devez les spécifier dans l'ordre où ils sont listés ici) :

- `--source` : le chemin vers le fichier de mission dans lequel injecter la météo.

- `--target` : le chemin vers le fichier de mission à créer avec la météo injectée. Avec la commande `injectall`, "${version}" sera remplacé par le nom de la version en cours de génération.

De plus, la commande `injectall` doit avoir l'option `--configuration` qui pointe vers le fichier de configuration des versions. Encore une fois, c'est un argument positionnel, donc n'utilisez pas le nom de l'option.

Exemple :

```cmd
veaf-tools inject source.miz target.miz 
```

ou

```cmd
veaf-tools injectall source.miz target-${version}.miz versions.json
```

#### Options facultatives en ligne de commande

Les options suivantes sont facultatives et sont disponibles pour les commandes `inject` et `injectall` :

- `--verbose` : si défini, l'outil affichera plus d'informations sur ce qu'il fait.

- `--quiet` : si défini, l'outil affichera moins d'informations sur ce qu'il fait.

- `--nocache` : si défini, l'outil n'utilisera pas le cache pour les fichiers météo. C'est utile si vous voulez forcer l'outil à récupérer la météo depuis l'API CheckWX à chaque exécution.

#### Options communes

La commande `injectall` finit par appeler le même code que la commande `inject` pour injecter la météo dans un fichier de mission cible, avec les options définies dans chaque cible du fichier de configuration.

Par conséquent, toutes les options qui peuvent être définies dans chaque cible du fichier de configuration pour la commande `injectall` peuvent aussi être définies comme options en ligne de commande pour la commande `inject`.

Voici les options disponibles, avec à chaque fois l'option en ligne de commande suivie de l'option cible correspondante :

- `--real`, `realweather` : si défini, la météo sera récupérée du monde réel via CheckWX (voir ["Injection de météo réelle"](#injection-de-météo-réelle)).

- `--clearsky`, `clearsky` : si défini, et si la météo du monde réel est récupérée, la couverture nuageuse sera limitée à trois octas. Cela permet d'avoir une météo réelle, mais assez claire pour l'Appui Aérien Rapproché.

- `--metar`, `metar` : si défini avec un [METAR](https://fr.wikipedia.org/wiki/METAR) valide, la météo sera générée à partir du METAR analysé (ex : *UG27 221130Z 04515KT +SHRA BKN008 OVC024 Q1006 NOSIG*)

- `--start`, `time` : l'heure de départ de la mission en secondes après minuit

- `--date`, `date` : la date de départ de la mission, avec une heure optionnelle (ex : `20230126` ou `202301260635` pour 6h35)

- `--variable`, `variableForMetar` : le nom de la variable qui sera remplacée par le METAR récupéré depuis CheckWX ; c'est une fonctionnalité utile pour afficher la météo dans le briefing.

- `--weather`, `weatherFile` : le chemin vers le fichier météo DCS à utiliser comme définition de météo statique.

- `--dontSetToday`, `dontSetToday` : si défini, la date de la mission ne sera pas définie à la date d'aujourd'hui.

- `--dontSetTodayYear`, `dontSetTodayYear` : si défini avec une année valide, et si dontSetToday est défini à `false`, l'année de la mission sera définie à l'année spécifiée tandis que le reste de la date sera défini à la date d'aujourd'hui.

## Fichier de versions

Le fichier de versions est un fichier JSON qui contient les conditions météorologiques et de départ que vous souhaitez injecter dans le fichier de mission lors de l'utilisation de la commande `injectall`.

Il contient plusieurs sections :

- `position` : les coordonnées de la mission ; utilisées pour calculer l'heure du coucher et du lever du soleil.

- `moments` : un tableau de moments, chaque moment définissant une heure et une date spécifiques qui peuvent être utilisées dans la section `targets`.
Ils sont définis avec des expressions JavaScript et des valeurs temporelles ; vous pouvez utiliser les variables  *sunset* et *sunrise* (par exemple, 3h après le coucher du soleil : `sunset + 3*60`, ou 15 minutes après 21h : `21:15`).
Par défaut, ces moments sont déjà définis :

  - night: 2 a.m.
  - beforedawn: 1:30 a.m. au lever du soleil
  - sunrise: lever du soleil
  - dawn: 0:30 a.m. après le lever du soleil
  - morning: 1:30 a.m. après le lever du soleil
  - day: 3 p.m.
  - beforesunset: 1:30 a.m. au coucher du soleil
  - sunset: coucher du soleil

- `targets` : un tableau de cibles, chaque cible contenant la météo et les conditions de départ qui seront utilisées pour créer une version spécifique du fichier de mission.

Chaque cible peut contenir les options énumérées [voir ici](#options-communes), et doit définir le nom de la version qui sera générée avec `version` (utilisé pour créer le nom du fichier de mission ; ex : `ma-mission-beforedawn-real-clear.miz` à partir de `ma-mission.miz`).

Exemple d'un fichier de versions :

```json
{
  "variableForMetar": "METAR",
  "moments": 
    {
      "onehour_tosunrise" : "sunrise-60*60",
      "late_morning" : "sunrise+120*60"
    },
  "position": 
    {
      "lat": 42.355691,
      "lon": 43.323853,
      "tz": "Asia/Tbilisi"
    },
  "targets": [
    {
      "version": "beforedawn-real-clear",
      "realweather": true,
      "clearsky": true,
      "moment": "beforedawn"
    },
    {
      "version": "beforesunrise-real",
      "realweather": true,
      "moment": "onehour_tosunrise",
      "date": "20230126"
    },
    {
      "version": "dawn-broken",
      "weatherfile": "broken-1.lua",
      "date": "202301260617"
    },
    {
      "version": "dawn-crosswind-vaziani",
      "weather": "UG27 221130Z 04515KT CAVOK Q1020 NOSIG",
      "time": "20680"
    }
  ]
}
```

## Fichier de configuration

Le fichier de configuration est situé dans le répertoire de travail de l'outil, et est nommé `configuration.json`.

Il sera automatiquement créé la première fois que vous exécuterez l'outil, et contient les sections suivantes :

- `theatres` : une liste de théâtres, avec les coordonnées où la météo sera recherchée avec CheckWX.

- `cacheFolder` : le dossier où les fichiers de cache météo seront stockés.

- `maxAgeInHours` : l'âge maximum des fichiers de cache météo, en heures.

- `checkwx_apikey` : la clé API à utiliser pour récupérer la météo depuis CheckWX. Obtenez-en une [voir ici](https://www.checkwxapi.com/).

Un exemple de fichier de configuration :

```json
{
  "theatres": {
    "caucasus": {
      "lat": 42.355691,
      "lon": 43.323853
    },
    "persiangulf": {
      "lat": 26.304151,
      "lon": 56.378506
    },
    "nevada": {
      "lat": 36.145615,
      "lon": -115.187618
    },
    "normandy": {
      "lat": 49.183336,
      "lon": -0.365908
    },
    "syria": {
      "lat": 32.666667,
      "lon": 35.183333
    },
    "marianaislands": {
      "lat": 14.079866,
      "lon": 145.15311411102653
    }
  },
  "checkwx_apikey": "53506465454660465040465",
  "cacheFolder": "./cache",
  "maxAgeInHours": 1
}
```

## Injection de météo réelle

C'est le comportement par défaut s'il n'y a pas de METAR ni de fichier météo DCS spécifié dans les options.

La météo sera récupérée depuis l'aéroport le plus proche des coordonnées du théâtre de la mission définies dans le fichier [`configuration.json`](#fichier-de-configuration).

L'outil utilise l'API CheckWX pour récupérer la météo ; vous devez vous inscrire sur CheckWX et obtenir une clé API gratuite (voir [voir ici](https://www.checkwxapi.com/)), et la stocker dans le fichier [`configuration.json`](#fichier-de-configuration).

La météo récupérée sera stockée dans un fichier de cache, afin que l'outil n'ait pas à récupérer la météo à chaque exécution. Cela évite de surcharger l'API CheckWX.

L'emplacement du cache, ainsi que le temps d'expiration du cache, peuvent être configurés dans le fichier [`configuration.json`](#fichier-de-configuration).

Un exemple d'utilisation de `inject` pour injecter une météo réelle :

```cmd
veaf-tools inject my-mission.miz my-mission-real.miz --real
```

Un exemple d'utilisation de `injectall` pour injecter une météo réelle :

```json
{
  "variableForMetar": "METAR",
  "position": 
    {
      "lat": 42.355691,
      "lon": 43.323853,
      "tz": "Asia/Tbilisi"
    },
  "targets": [
    {
      "version": "beforedawn-real-clear",
      "realweather": true,
      "clearsky": true,
      "moment": "beforedawn"
    },
    {
      "version": "dawn-real",
      "realweather": true,
      "moment": "dawn"
    }
  ]
}
```

```cmd
veaf-tools injectall my-mission.miz my-mission-${version}.miz versions.json
```

## Injection d'une météo prédéfinie

En utilisant soit un METAR soit un fichier météo DCS, vous pouvez injecter une météo prédéfinie dans le fichier de mission.

Vous pouvez extraire la définition de la météo d'une mission DCS en éditant le fichier `mission` qui est stocké à l'intérieur du fichier ".miz" (indice : c'est une archive ZIP), et en cherchant la section `["weather"]`. Écrivez cette section dans un fichier LUA, et utilisez-le comme paramètre `--weather` ou option `weatherFile`.

Voici un exemple de définition de météo DCS :

```lua
["weather"] = {
 ["atmosphere_type"] = 0,
    ["clouds"] = 
    {
        ["thickness"] = 200,
        ["density"] = 0,
        ["preset"] = "Preset13",
        ["base"] = 3400,
        ["iprecptns"] = 0,
    }, -- end of ["clouds"]
    ["cyclones"] = {
 }, -- end of ["cyclones"]
 ["dust_density"] = 0,
 ["enable_dust"] = false,
 ["enable_fog"] = false,
 ["fog"] = {
   ["thickness"] = 0,
   ["visibility"] = 0,
 }, -- end of ["fog"]
 ["groundTurbulence"] = 26.656422237728,
 ["qnh"] = 758.444,
 ["season"] = {
   ["temperature"] = 23.200000762939,
 }, -- end of ["season"]
 ["type_weather"] = 2,
 ["visibility"] = {
   ["distance"] = 1593,
 }, -- end of ["visibility"]
 ["wind"] = {
   ["at2000"] = {
     ["dir"] = 148,
     ["speed"] = 10.604474819794,
   }, -- end of ["at2000"]
   ["at8000"] = {
     ["dir"] = 160,
     ["speed"] = 12.07985101455,
   }, -- end of ["at8000"]
   ["atGround"] = {
     ["dir"] = 150,
     ["speed"] = 4.5,
   }, -- end of ["atGround"]
 }, -- end of ["wind"]
}, -- end of ["weather"]
```

```cmd
veaf-tools inject my-mission.miz my-mission-real.miz --weather scattered-rain.lua
```

Ou, si vous utilisez `injectall` :

```json
{
  "variableForMetar": "METAR",
  "position": 
    {
      "lat": 42.355691,
      "lon": 43.323853,
      "tz": "Asia/Tbilisi"
    },
  "targets": [
    {
      "version": "dawn-broken",
      "weatherfile": "broken-1.lua",
      "moment": "dawn"
    }
  ]
}
```

```cmd
veaf-tools injectall my-mission.miz my-mission-${version}.miz versions.json
```

Utiliser un METAR est plus simple, car vous pouvez l'obtenir sur internet. Voici un exemple :

```cmd
veaf-tools inject my-mission.miz my-mission-real.miz --metar "UG27 221130Z 04515KT CAVOK Q1020 NOSIG"
```

Ou, si vous utilisez `injectall` :

```json
{
  "variableForMetar": "METAR",
  "position": 
    {
      "lat": 42.355691,
      "lon": 43.323853,
      "tz": "Asia/Tbilisi"
    },
  "targets": [
    {
      "version": "dawn-crosswind-vaziani",
      "weather": "UG27 221130Z 04515KT CAVOK Q1020 NOSIG",
      "moment": "dawn"
    }
  ]
}
```

```cmd
veaf-tools injectall my-mission.miz my-mission-${version}.miz versions.json
```

## Contacts

Si vous avez besoin d'aide, ou si vous voulez suggérer quelque chose, vous pouvez :

- contacter **Zip** sur [GitHub][Zip on Github] ou sur [Discord][Zip on Discord]
- aller consulter le [site de la VEAF][VEAF website]
- poster sur le [forum de la VEAF][VEAF forum]
- rejoindre le [Discord de la VEAF][VEAF Discord]

[VEAF Discord]: https://www.veaf.org/discord
[Zip on Github]: https://github.com/davidp57
[Zip on Discord]: https://discordapp.com/users/421317390807203850
[VEAF website]: https://www.veaf.org
[VEAF forum]: https://www.veaf.org/forum

[veaftools-inject-options]: ../images/veaftools-inject-options.png
[veaftools-injectall-options]: ../images/veaftools-injectall-options.png
