---
title: Site de documentation des outils et scripts VEAF
description: Documentation des outils et scripts VEAF pour les créateurs de mission, utilisateurs et programmeurs
---

Navigation: vous êtes sur la page principale

-----------------------------

🚧 **TRAVAUX EN COURS** 🚧

La documentation est en train d'être retravaillée, morceau par morceau.
En attendant, vous pouvez consulter l'[ancienne documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

## Table des matières du site complet

### Par rôle

En fonction de votre rôle, vous pourrez trouver une page dédiée qui résume tous les éléments intéressants ici:

- créateur de mission - [ici](./mission-maker/index.md)
- pilote - [En cours d'écriture](./wip.md) <!--- TODO écrire la page -->
- administrateur de serveur - [En cours d'écriture](./wip.md) <!--- TODO écrire la page -->
- développeur courageurs - [En cours d'écriture](./wip.md) <!--- TODO écrire la page -->

### Par sujet

- installer et utiliser les outils VEAF (*pour tous*) - [ici](./tools/index.md)
- configurer un environnement de créateur de mission VEAF (*pour créateurs de missions*) - [ici](./environment/index.md)
- découvrir les fonctionnalités pour créateurs de mission (*pour créateurs de missions*) - [ici](./mission-maker/index.md)
- apprendre à programmer les scripts en Lua (*pour programmeurs*) - [ici](./programmer/index.md)

## Table des matières de cette page

- [Introduction](#introduction)
- [De quoi ai-je besoin pour commencer ?](#de-quoi-ai-je-besoin-pour-commencer-)
- [Quels outils existent et comment les utiliser ?](#quels-outils-existent-et-comment-les-utiliser-)
- [Comment utiliser les scripts VEAF dans une mission que je veux concevoir ?](#comment-utiliser-les-scripts-veaf-dans-une-mission-que-je-veux-concevoir-)
- [Comment contribuer à ce merveilleux dépôt ?](#comment-contribuer-à-ce-merveilleux-dépôt-)
- [Je veux aider à maintenir la documentation](#je-veux-aider-à-maintenir-la-documentation)
- [Je dois ajouter de nouvelles fonctionnalités ou corriger des bugs dans les scripts](#je-dois-ajouter-de-nouvelles-fonctionnalités-ou-corriger-des-bugs-dans-les-scripts)
- [Contacts](#contacts)

## Introduction

Les Outils VEAF pour la Création de Mission (VEAF Mission Creation Tools ou VMCT) ont été écrits et sont maintenus par la VEAF (Virtual European Air Force).

Ils sont composés d'outils et de scripts conçus pour créer, partager et maintenir facilement des missions dynamiques:

<!--- TODO ajouter des liens directs dans la liste ci-dessous quand tout sera traduit -->

- des outils pour manipuler les fichiers de mission DCS et les serveurs
- les scripts de mission VEAF (organisés en modules)
- les hooks serveur VEAF
- certains scripts communautaires, parfois édités par VEAF (ex. CTLD, MiST)
- un flux de travail simple pour la création, l'édition et la publication de missions
- des outils pour supporter ce flux, y compris un convertisseur qui génère une mission dynamique à partir d'une mission statique existante
- cette documentation

Nos dépôts GitHub :

- le [dépôt principal][VEAF-Mission-Creation-Tools-repository] contient toutes les sources et la base de documentation
- le [convertisseur de missions][VEAF-mission-converter-repository] peut être forké ou téléchargé pour injecter les scripts et outils dans une mission existante
- la [mission démo][VEAF-demo-mission-repository] (fork ou téléchargement) est une petite mission simple qui utilise certaines des dernières fonctionnalités des outils
- la mission d'entraînement VEAF [Caucasus training mission][VEAF-Open-Training-Mission-repository] (fork ou téléchargement) est un bon exemple de travail des scripts dans une mission complexe
- le [dépôt de missions multijoueurs][VEAF-Multiplayer-Missions-repository] contient des missions avec lesquelles nous avons joué (certaines peuvent être anciennes et obsolètes)

## De quoi ai-je besoin pour commencer ?

Vous devrez configurer un environnement sur votre PC avec des logiciels spécifiques (gratuits).

Lisez cette [page](./environment/index.md) pour plus d'informations.

## Quels outils existent et comment les utiliser ?

Les Outils de Création de Missions VEAF offrent de nombreux outils et scripts.

La plupart sont destinés à être utilisés dans le pipeline de création de mission (c.-à-d. par un créateur de mission travaillant sur une mission, lire la [documentation pour créateurs de mission](./mission-maker/index.md)), mais certains peuvent être utilisés comme outils autonomes :

- le normaliseur de dictionnaires LUA [En cours d'écriture](./wip.md) <!--- TODO écrire la page --> qui facilite la comparaison des fichiers LUA
- l'[Injecteur Météo](./tools/veaf-tools-weather-injector.md) qui peut générer plusieurs fichiers de mission avec des heures de départ et conditions météorologiques différentes à partir d'un modèle
- le [Sélecteur de Mission](./tools/veaf-tools-mission-selector.md) qui choisit une mission de départ pour votre serveur dédié à partir d'une liste de missions et d'un planning.

## Comment utiliser les scripts VEAF dans une mission que je veux concevoir ?

Veuillez lire la [documentation pour créateurs de mission](./mission-maker/index.md).

Pour un démarrage rapide, forkez ou téléchargez le [convertisseur de missions](https://github.com/VEAF/VEAF-mission-converter) et suivez les instructions du fichier `readme.md`. Vous apprendrez comment utiliser les Outils de Création de Missions VEAF dans votre propre mission existante.

Vous pouvez également "forker" ou télécharger la [mission démo](https://github.com/VEAF/VEAF-Demo-Mission) pour voir ce qui est possible (généralement seules les fonctionnalités récentes y sont démontrées), ainsi que la [mission d'entraînement VEAF Caucasus](https://github.com/VEAF/VEAF-Open-Training-Mission) qui est une mission d'entraînement très complète, ouverte et dynamique utilisant de nombreuses fonctionnalités.

## Comment contribuer à ce merveilleux dépôt ?

D'abord, merci !

Nous accueillons toujours de l'aide et de nouvelles idées.

Veuillez toujours utiliser des branches et des pull requests ! Commencez par forker le dépôt VEAF-Mission-Creation-Tools [principal](https://github.com/VEAF/VEAF-Mission-Creation-Tools), créez une branche, modifiez et publiez votre travail.

## Je veux aider à maintenir la documentation

La façon la plus simple est d'éditer les fichiers directement sur le site GitHub.

Mais vous pouvez aussi forker le [dépôt principal][VEAF-Mission-Creation-Tools-repository].

## Je dois ajouter de nouvelles fonctionnalités ou corriger des bugs dans les scripts

Veuillez lire la [documentation pour programmeurs](./programmer/index.md).

## Contacts

Si vous avez besoin d'aide, ou si vous voulez suggérer quelque chose, vous pouvez :

- contacter **Zip** sur [GitHub][Zip on Github] ou sur [Discord][Zip on Discord]
- aller consulter le [site de la VEAF][VEAF website]
- poster sur le [forum de la VEAF][VEAF forum]
- rejoindre le [Discord de la VEAF][VEAF Discord]

[Badge-Discord]: https://img.shields.io/discord/471061487662792715?label=VEAF%20Discord&style=for-the-badge
[VEAF-logo]: ../images/logo.png

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
