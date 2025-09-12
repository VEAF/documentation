---
description: Permet de sélectionner une mission sur un serveur, en se basant sur un calendrier
---

# L'outil "Sélecteur de Mission"

Navigation: [Site de documentation VEAF - page principale](../index.md) > [Outils de Création de Mission](./index.md) > [Application *veaf-tools*](./veaf-tools.md)

-----------------------------

🚧 **TRAVAUX EN COURS** 🚧

La documentation est en cours de révision, partie par partie.
En attendant, vous pouvez consulter l'[ancienne documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

# Table des matières

- utiliser le Sélecteur de Mission - [voir ici](#utiliser-le-sélecteur-de-mission)
- préparer le fichier de configuration du serveur - [voir ici](#configuration-serveur-par-défaut)
- configurer le fichier de définition du planning - [voir ici](#fichier-de-définition-du-planning)
- utiliser l'assistant Google Sheet - [voir ici](#assistant-google-sheet)

# Introduction

Le Sélecteur de Mission fait partie de l'application VEAF Tools. Consultez l'installation et la description dans la [documentation générale de l'application VEAF Tools](./veaf-tools.md).

# Utiliser le Sélecteur de Mission

Le but du sélecteur de mission est de choisir une mission basée sur un planning que vous fournirez, et de configurer votre serveur pour démarrer avec la mission sélectionnée.

Par conséquent, certains prérequis sont nécessaires :

- vous devez avoir un [serveur DCS dédié](https://www.digitalcombatsimulator.com/en/downloads/world/server_beta/) configuré sur votre machine
- vous avez besoin d'une liste de missions que vous voulez utiliser (une sorte de bibliothèque) ; vous pouvez utiliser l'[Injecteur Météo](./veaf-tools-weather-injector.md#using-the-weather-injector) pour générer des missions avec différentes conditions météorologiques basées sur un seul modèle
- vous avez besoin d'un planning qui définit quelle mission exécuter à quel moment ; vous devriez y réfléchir à l'avance ! Voici un [Google Sheet][veaf-mission-selector-helper-google-sheet] qui vous aidera à définir votre planning, générer le fichier de définition du planning, et construire la liste des missions du serveur DCS.

Le fonctionnement est assez simple : vous configurez un fichier `serverSettings-default.lua`. Ce fichier est identique au classique `serverSettings.lua` ; il contient la liste de toutes vos missions existantes (il peut facilement devenir énorme, avec toutes les conditions météo et de départ !)

L'outil Sélecteur de Mission lit le planning, décide quelle mission il va sélectionner, et la recherche dans le fichier `serverSettings-default.lua`. S'il la trouve, il crée un fichier `serverSettings.lua` avec les propriétés `current` et `listStartIndex` définies sur l'index correct pour la mission sélectionnée ; la prochaine fois que le serveur démarre, il chargera automatiquement cette mission.

## Options en ligne de commande

L'outil Sélecteur de Mission est conçu pour être lancé depuis la ligne de commande.

C'est en fait une commande spécifique de l'application *veaf-tools*.

```cmd
veaf-tools select-mission <source> <target> <configuration>
```

### Options obligatoires en ligne de commande

Les options suivantes sont obligatoires ; n'utilisez pas le nom de l'option, ce sont des arguments positionnels (c'est-à-dire que vous devez les spécifier dans l'ordre où ils sont listés ici) :

- `--source` : le chemin vers le fichier de configuration serveur par défaut. Ce fichier sera copié vers le fichier cible, et les propriétés `current` et `listStartIndex` seront mises à jour. Voir [Configuration serveur par défaut](#configuration-serveur-par-défaut).

- `--target` : le chemin vers le fichier de configuration serveur qui sera utilisé par le serveur. Ce fichier sera créé par l'outil.

- `--configuration` : le chemin vers le fichier de configuration du planning. Ce fichier sera lu par l'outil pour déterminer quelle mission sélectionner. Voir [Fichier de définition du planning](#fichier-de-définition-du-planning).

Exemple :

```cmd
veaf-tools select-mission serverSettings-default.lua serverSettings.lua schedule.json
```

### Options facultatives en ligne de commande

Les options suivantes sont facultatives et sont disponibles pour les commandes `inject` et `injectall` :

- `--verbose` : si défini, l'outil affichera plus d'informations sur ce qu'il fait.

- `--quiet` : si défini, l'outil affichera moins d'informations sur ce qu'il fait.

## Configuration serveur par défaut

Comme nous l'avons dit, c'est une copie du fichier principal `serverSettings.lua`, avec toutes les missions que vous voulez utiliser. [Voir ici][veaf-mission-selector-helper-example-serversettings] pour un exemple d'un tel fichier.

Il est très important que toutes les missions disponibles soient listées, et que les index du tableau des missions soient corrects. Vous pouvez utiliser l'[assistant Google Sheet][veaf-mission-selector-helper-google-sheet] onglet "Mission list helper" pour aider à renuméroter les missions.

## Fichier de définition du planning

Le fichier de définition du planning est un fichier JSON qui définit le planning de vos missions. [Voir ici][veaf-mission-selector-helper-example-cron] pour un exemple d'un tel fichier.

Il définit plusieurs éléments :

- le nom du serveur, dans l'élément `server` ; il n'est pas utilisé.
- la mission par défaut, dans l'élément `default` ; elle est utilisée pour sélectionner une mission si aucune autre mission ne correspond à l'heure actuelle dans le planning.
- la collection `moments` ; c'est le cœur du système ; nous allons le décrire ci-dessous.

Chaque moment dans la collection `moments` définit une mission à exécuter à un moment précis. Il a les éléments suivants :

- `//comment` : facultatif ; un commentaire pour décrire le moment
- `month` : le mois de l'année, de 1 à 12 ; vous pouvez utiliser `null` pour indiquer tous les mois, ou simplement omettre l'élément.
- `dayOfMonth` : le jour du mois, de 1 à 31 ; vous pouvez utiliser `null` pour indiquer tous les jours, ou simplement omettre l'élément.
- `dayOfWeek` : le jour de la semaine, de 1 à 7, ou en tant que chaîne (anglais ou français, soit le nom complet du jour soit les 3 premières lettres) ; vous pouvez utiliser `null` pour indiquer tous les jours, ou simplement omettre l'élément.
- `hour` : l'heure du jour, de 0 à 23, ou en tant que chaîne ("morning", "afternoon", "day", "night", "matin", "après-midi", "jour", "nuit") ; vous pouvez utiliser `null` pour indiquer toutes les heures, ou simplement omettre l'élément.
- `missions` : un tableau de missions parmi lesquelles choisir ; le sélecteur de mission en choisira une au hasard
- `mission` : une seule mission ; le sélecteur de mission utilisera cette mission

## Assistant Google Sheet

Nous avons créé cet [utile Google Sheet][veaf-mission-selector-helper-google-sheet] pour vous aider avec le fichier de définition du planning. Il a 3 onglets :

- `Calendar` : vous permet de définir le planning de vos missions en ajoutant une ligne pour chaque horaire.
- `Mission selector cron.json helper` : ici le fichier serverCron.json sera généré à partir des données saisies dans l'onglet "Calendar". Vous pouvez copier/coller le contenu de la colonne C dans votre fichier de définition du planning (veuillez supprimer la virgule supplémentaire à la fin de la dernière ligne "moment")
- `Mission list helper` : avec cet outil, vous pouvez facilement renuméroter une liste modifiée de missions dans le fichier `serverSettings-default.lua`. Commencez par coller votre code lua de liste de missions existante (même si elle n'est pas numérotée correctement) dans la colonne A ; vous pouvez ensuite copier/coller le contenu de la colonne C dans votre fichier `serverSettings-default.lua`, il est renuméroté correctement.

Voici [un exemple][veaf-mission-selector-helper-example-cron-generated] de fichier de définition du planning généré.

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

[veaf-mission-selector-helper-google-sheet]: https://docs.google.com/spreadsheets/d/1DP78g43NsmC7hjyrVWKnPd7xNSb1vUi5PIRP9vxmfjo

[veaf-mission-selector-helper-example-serversettings]: ../examples/serverSettings-default.lua
[veaf-mission-selector-helper-example-cron]: ../examples/serverCron-example.json
[veaf-mission-selector-helper-example-cron-generated]: ../examples/serverCron-example_generated.json