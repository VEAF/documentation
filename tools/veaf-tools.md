---
description: Outil principal permettant d'interagir avec des missions DCS
---

# L'application NodeJS *veaf-tools*

Navigation: [Site de documentation VEAF - page principale](../index.md) > [Outils de Création de Mission](./index.md)

-----------------------------

🚧 **TRAVAUX EN COURS** 🚧

La documentation est en cours de révision, partie par partie.
En attendant, vous pouvez consulter l'[ancienne documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

## Table des matières

- cas d'utilisation complet (serveurs publics VEAF) - [voir ici](#cas-dutilisation)
- installer et utiliser l'application *veaf-tools* - [voir ici](#installation)
- configurer et utiliser l'Injecteur Météo - [voir ici](veaf-tools-weather-injector.md)
- configurer et utiliser le Sélecteur de Mission - [voir ici](veaf-tools-mission-selector.md)

## Introduction

Cette application NodeJS est une collection d'outils qui peuvent être utilisés pour manipuler les missions.

Actuellement, elle contient les outils suivants :

- Injecteur météo
- Sélecteur de mission

## Cas d'utilisation

La VEAF utilise ces outils avec des scripts lua de hook serveur et des watchdogs Powershell (tous disponibles sur [ce dépôt GitHub][VEAF-Servers-Public-repository]) pour avoir nos serveurs fonctionnant 24/7, et redémarrant automatiquement en cas de crash.

Toutes les deux heures, si personne n'est connecté (ou quand le dernier joueur se déconnecte), le serveur redémarre.

Le script de démarrage appelle l'outil d'injection météo, qui générera un ensemble de missions pour ce serveur, avec différentes conditions météorologiques (y compris la météo réelle du moment).

Ensuite, il appelle l'outil de sélection de mission, qui sélectionnera la mission à démarrer selon l'heure de la journée et le planning :

[![veaf-servers-schedule]][veaf-servers-schedule]

Cela garantit que nos serveurs sont toujours opérationnels et configurés avec la mission que nous avons collectivement décidé de charger pour ce moment.

### Injecteur météo

L'Injecteur météo est un outil qui transforme un fichier de mission unique en une collection de missions, avec le même contenu mais des conditions météorologiques et de départ différentes.

Il peut être utilisé pour injecter une définition météo DCS prédéfinie, lire un METAR et générer une mission avec la météo correspondante, ou même utiliser la météo du monde réel.

Il peut également créer différentes heures et dates de départ pour la mission, soit avec des valeurs absolues (par exemple, le 26/01/2023 à 14h20), soit avec des "moments" prédéfinis (par exemple, deux heures après le coucher du soleil).

C'est un outil très utile à utiliser avec un serveur qui fonctionne 24/7 et qui doit avoir des conditions météorologiques différentes à chaque fois qu'il démarre la même mission.

[Vidéo de démonstration][veaftools-injectall-demo]

### Sélecteur de mission

Le sélecteur de mission est utilisé pour démarrer un serveur dédié avec une mission spécifique, selon un planning défini dans un fichier de configuration.

## Installation

C'est un outil autonome, il ne nécessite pas d'environnement VEAF Mission Creation Tools spécifique (comme décrit [ici](..\environment\index.md)).

Il est donc très facile à installer sur un serveur ou sur votre propre ordinateur.

***Nota bene : ce chapitre est également disponible en [tutoriel vidéo][install-chocolatey-nodejs-veaftools]***

Vous devrez installer ces outils sur votre ordinateur :

- *NodeJS* : vous avez besoin de NodeJS pour exécuter les programmes JavaScript dans les outils de création de mission VEAF ; voir [ici](https://nodejs.org/fr/)
- *yarn* : vous avez besoin du gestionnaire de paquets Yarn pour récupérer et mettre à jour les outils de création de mission VEAF ; voir [ici](https://yarnpkg.com/fr/)

### Installer les outils avec Chocolatey

Les outils requis peuvent facilement être installés en utilisant *Chocolatey* (voir [ici](https://chocolatey.org/)).

Pour installer Chocolatey, utilisez cette commande dans une invite Powershell en mode administrateur :

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

Une fois *Chocolatey* installé, installez NodeJS en tapant cette simple commande dans une invite de commande :

```cmd
choco install -y nodejs
```

Puis fermez et rouvrez l'invite de commande.

### Installer l'application veaf-tools

Dans une invite de commande, tapez :

```cmd
npm install -g veaf-mission-creation-tools
```

Puis fermez et rouvrez l'invite de commande.

## Utilisation générale de l'application

Pour exécuter les outils VEAF, tapez simplement `veaf-tools` dans une invite de commande.

[![veaftools-options]][veaftools-options]

## Mise à jour de l'application

Pour mettre à jour les outils VEAF, faites simplement la même chose que pour l'installation.

Dans une invite de commande, tapez :

```cmd
npm install -g veaf-mission-creation-tools
```

NPM récupérera et installera automatiquement la dernière version.

## Utiliser l'Injecteur Météo

Veuillez consulter la [documentation de l'Injecteur Météo](./veaf-tools-weather-injector.md) pour plus d'informations.

## Utiliser le Sélecteur de Mission

Veuillez consulter la [documentation du Sélecteur de Mission](./veaf-tools-mission-selector.md) pour plus d'informations.

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

[install-chocolatey-nodejs-veaftools]: ../images/install-chocolatey-nodejs-veaftools.mp4
[veaftools-options]: ../images/veaftools-options.png
[veaf-servers-schedule]: ../images/veaf-servers-schedule.png
[veaftools-injectall-demo]: ../images/veaftools-injectall-demo.mp4

[VEAF-Servers-Public-repository]: https://github.com/VEAF/VEAF-Servers-Public
