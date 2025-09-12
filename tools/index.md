# Documentation des outils VEAF pour les créateurs de mission et les administrateurs de serveur

Navigation: [Site de documentation VEAF - page principale](../index.md)

-----------------------------

🚧 **TRAVAUX EN COURS** 🚧

La documentation est en cours de révision, partie par partie.
En attendant, vous pouvez consulter l'[ancienne documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

## Table des matières

- configurer et utiliser l'application *veaf-tools* - [voir ici](veaf-tools.md)
- configurer et utiliser l'Injecteur Météo - [voir ici](veaf-tools-weather-injector.md)
- configurer et utiliser le Sélecteur de Mission - [voir ici](veaf-tools-mission-selector.md)
- configurer et utiliser le normalisateur de dictionnaire LUA - [En cours d'écriture](./wip.md) <!--- TODO écrire la page -->

## Introduction

Les Outils de Création de Mission VEAF fournissent des scripts qui rendent les missions dynamiques, ainsi que des outils utilisés pour manipuler les missions.

## Application *veaf-tools*

Cette application NodeJS est une collection d'outils qui peuvent être utilisés pour manipuler les missions.

Actuellement, elle contient les outils suivants :

- Injecteur météo
- Sélecteur de mission

Consultez la [page de documentation de l'application VEAF Tools](veaf-tools.md) pour plus de détails.

### Injecteur météo

L'Injecteur météo est un outil qui transforme un fichier de mission unique en une collection de missions, avec le même contenu mais des conditions météorologiques et de départ différentes.

Il peut être utilisé pour injecter une définition météo DCS prédéfinie, lire un METAR et générer une mission avec la météo correspondante, ou même utiliser la météo du monde réel.

Il peut également créer différentes heures et dates de départ pour la mission, soit avec des valeurs absolues (par exemple, le 26/01/2023 à 14h20), soit avec des "moments" prédéfinis (par exemple, deux heures après le coucher du soleil).

C'est un outil très utile à utiliser avec un serveur qui fonctionne 24/7 et qui doit avoir des conditions météorologiques différentes à chaque fois qu'il démarre la même mission.

Consultez la [page de documentation de l'application VEAF Tools](veaf-tools.md) pour plus de détails.

### Sélecteur de mission

Le sélecteur de mission est utilisé pour démarrer un serveur dédié avec une mission spécifique, selon un planning défini dans un fichier de configuration.

Consultez la [page de documentation de l'application VEAF Tools](veaf-tools.md) pour plus de détails.

## Normalisateur de dictionnaire LUA

<!--- TODO écrire un résumé -->

La documentation complète est disponible - [En cours d'écriture](./wip.md) <!--- TODO écrire la page -->

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