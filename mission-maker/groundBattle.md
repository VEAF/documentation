---
description: Gestion des groupes de combat terrestre
---

# Module GroundAI

Navigation: [Page dédiée aux créateurs de mission du site de documentation VEAF](./index.md)

-----------------------------

🚧 **TRAVAUX EN COURS** 🚧

La documentation est en train d'être retravaillée, morceau par morceau.
En attendant, vous pouvez consulter l'[ancienne documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

## Table des matières

- Introduction - [ici](#introduction)
- Principes et fonctionnement - [ici](#utilisation)
- Mise en place - [ici](#mise-en-place)
- Configuration dans MissionConfig ou dans des triggers [ici](#configuration)

### Introduction

Ce module a pour but de permettre la gestion de groupes de combat terrestres; il offre:

- la définition d'un groupe de combat (BG) - c'est un ensemble d'unités terrestres (plusieurs groupes DCS) qui prennent part à une bataille terrestre
- le placement de zones de dépose (DZ) et de points de ralliement (RP), de lignes et de points de bataille (BL, BP)
- l'obtention d'informations concernant la bataille en cours via le menu radio ou les commandes remote (chat)
- la gestion plus "intelligente" du comportement des unités au sol:
  - en cas de contact, elles peuvent envoyer un message radio, tirer des grenades fumigènes et battre en retraite - ou au contraire, attaquer si elles considèrent qu'elles sont plus fortes que l'ennemi
  - l'assistance au CAS via une communication de type JTAC
  - la retraite en ordre quand les risques sont trop élevés
  - (optionnellement) la réduction des dégats dûs aux tirs
  - (optionnellement) avoir un comportement plus réaliste sous le feu (à la fois les unités alliées et ennemis "baissent la tête" et réduisent leur activité)

(Veuillez lire [Mission Maker](./index.md)).

### Utilisation

**TODO**

### Mise en place

**TODO**

### Configuration

**TODO**

Dans le fichier *missionConfig.lua*, il faut activer le module *veafGroundBattle* et l'initialiser:

```lua
if veafGroundBattle then
    veaf.loggers.get(veaf.Id):info("init - veafGroundBattle")
    veafGroundBattle.initialize()
end
```

### Contacts

Si vous avez besoin d'aide, ou si vous voulez suggérer quelque chose, vous pouvez :

- contacter **Rex** sur [GitHub][Rex on Github] ou sur [Discord][Rex on Discord]
- contacter **Zip** sur [GitHub][Zip on Github] ou sur [Discord][Zip on Discord]
- aller consulter le [site de la VEAF][VEAF website]
- poster sur le [forum de la VEAF][VEAF forum]
- rejoindre le [Discord de la VEAF][VEAF Discord]

[VEAF Discord]: https://www.veaf.org/discord
[Zip on Github]: https://github.com/davidp57
[Zip on Discord]: https://discordapp.com/users/421317390807203850
[Rex on Github]: https://github.com/RexAttaque
[Rex on Discord]: https://discordapp.com/users/256509086777081856
[VEAF website]: https://www.veaf.org
[VEAF forum]: https://www.veaf.org/forum


