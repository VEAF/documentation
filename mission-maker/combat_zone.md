---
description: Mettre en place une zone de combat air-sol
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
- Configuration - [ici](#comment-configurer-une-combat-zone)
- Exemples - [ici](#exemples-complets)

# Introduction

Le module *Cobat Zone* permet de créer facilement des zones qu'on peut déclencher à la demande, et qui peuvent contenir des groupes DCS (unités sol, navires, hélicoptères) - avec leurs waypoints et leurs instructions, des commandes VEAF (comme celles qu'on peut déclencher dans un marqueur sur la carte F10).

# Principes

Une zone *Combat Zone* est constituée de :
- une zone, définie par une trigger zone DCS placée dans l'éditeur de mission (circulaire ou quadpoint)
- un ou des groupes DCS ou commandes VEAF
- des paramètres (dans `missionConfig.lua`)

TODO - exemples avec screenshots

Une fois configurée, une entrée sera ajoutée dans le menu radio VEAF, comprenant ces commandes:
- "Get info": obtenir des informations sur la zone et son état. En particulier:
  - le briefing configuré dans les paramètres
  - les unités alliées restant dans la zone (y compris le détail des unités - type, nombre - si la zone est paramétrée en "training")
  - les unités ennemies restant dans la zone (y compris le détail des unités - type, nombre - si la zone est paramétrée en "training")
  - les coordonnées du centre de la zone
  - la météo sur la zone
- "Activate zone" si la zone n'est pas encore active
- "Desactivate zone" si la zone est active
- "Request RED smoke on target" pour demander une fumée rouge sur le centre de la zone (ou l'épicentre des ennemis restants, si la zone est paramétrée en "training")
- "Request illumination flare on target" pour demander une fusée d'éclairage sur le centre de la zone (ou l'épicentre des ennemis restants, si la zone est paramétrée en "training")

Le menu radio est sécurisé ; par défaut il faut être connecté (`/secu login`) pour pouvoir activer ou désactiver des zones (sauf celles qui sont en mode "entrainement").

# Comment déclarer une *Combat Zone* dans le fichier de configuration

Tout commence dans le fichier de configuration de la mission `missionConfig.lua`, qui est situé dans le répertoire `src/scripts` de votre mission.

Ce dernier est un fragment de code source [LUA](https://fr.wikipedia.org/wiki/Lua) qui permet de choisir la manière dont les scripts VEAF sont activés et configurés. Nous vous conseillons d'utiliser Notepad++ ou Visual Studio Code pour l'éditer.

Par défaut, si vous avez utilisé le [convertisseur de mission existante][VEAF-mission-converter-repository] pour préparer votre dossier de mission VEAF, il contient déjà un modèle de configuration QRA que vous pourrez trouver en cherchant `-- configure COMBAT ZONE`.

Le principe de configuration est simple.

Tout d'abord on crée un "objet" de type *VeafCombatZone* en appelant `AirWaVeafCombatZoneveZone:new()`. Cet appel renvoie une instance de *VeafCombatZone*, qu'on peut stocker dans une variable (`local maZone = VeafCombatZone:new()`) ou utiliser tout de suite avec une [désignation chaînée](https://fr.wikipedia.org/wiki/D%C3%A9signation_cha%C3%AEn%C3%A9e) (en enchainant les appels aux méthodes de configuration qui renvoient toutes, tour à tour, la même instance de *VeafCombatZone*).

<u>Exemple de chainage:</u>

```lua
VeafCombatZone:new()
    :setMissionEditorZoneName("combatZone_CrossKobuleti")
	:setFriendlyName("Cross Kobuleti")
```

L'avantage de cette méthode est sa simplicité.

<u>Exemple d'utilisation d'une variable:</u>

```lua
local zoneCrossKobuleti = VeafCombatZone:new()
zoneCrossKobuleti:setMissionEditorZoneName("combatZone_CrossKobuleti")
zoneCrossKobuleti:setFriendlyName("Cross Kobuleti")
```

L'avantage de la seconde méthode est qu'on peut, plus loin dans le fichier `missionConfig.lua`, utiliser une référence à la variable qu'on a définie pour accéder à l'instance de *VeafCombatZone* (par exemple, dans la définition d'un alias, d'une commande "mission maker", ou dans un menu radio).

## Structure d'une déclaration de zone *VeafCombatZone*

### Création de la zone

Création d'une nouvelle instance de *VeafCombatZone*: une instance définit une zone et son comportement; exemple: la zone d'entrainement de Kobuleti.

Utiliser soit une variable locale, soit une désignation chaînée (voir (ce chapitre)[#comment-configurer-une-zone-AirWaves]) pour exploiter cette instance.

```lua
VeafCombatZone:new()
```

### Paramètres obligatoires

Définition du nom technique de la zone: ce nom sert à retrouver la Combat Zone avec la commande `veafCombatZone.GetZone()`; exemple: `veafCombatZone.GetZone("combatZone_CrossKobuleti")`

```lua
:setMissionEditorZoneName("MinevcombatZone_CrossKobuletiody")
```

Description de la zone: c'est ce qui sera repris dans les différents messages et dans le menu radio

```lua
:setFriendlyName("Cross Kobuleti")
```

Définition du briefing:

```lua
:setBriefing("A BTR patrol and a few manpads are dispersed around the Batumi airbase")
```

Pour définir le briefing sur plusieurs lignes, le plus simple est d'utiliser les "double brackets":

```lua
:setBriefing([[This is a simple mission
You must destroy the comm antenna
The other ennemy units are secondary targets]])
```

### Paramètres optionnels

#### Mode "entrainement"

En mode entrainement, la zone est accessible à tous (pas de sécurisation du menu radio), les informations détaillent le nombre et le type d'unités restantes, et les balises fumigènes sont centrées sur le barycentre des ennemis restants (sinon, au centre de la zone).

```lua
:setTraining(true)
```

#### Désactivation du menu radio

Si on le souhaite, on peut désactiver les menus radio d'une Combat Zone.

```lua
:disableRadioMenu()
```

#### Nettoyage des carcasses

Par défaut, à la désactivation d'une zone, les carcasses de véhicules et les cadavres sont automatiquement nettoyés. Cela peut être désactivé:

```lua
:disableJunkCleanup()
```

#### Activation de la zone

On peut choisir de laisser les utilisateurs activer la zone, ou pas ; pour ça on utilise:

```lua
:enableUserActivation()
```

```lua
:disableUserActivation()
```

#### Fumigènes et fusées d'éclairage

Par défaut, ces options sont activées sur toutes les zones. On peut changer ça en utilisant `setEnableSmokeAndFlare` ; par exemple:

```lua
setEnableSmokeAndFlare(true)
```

#### Options relatives au menu radio

**Préfixe du menu radio**

On peut ajouter un préfixe aux entrées de menu radio, pour faciliter la lecture.

En effet, les zones actives sont marquées par un astérisque dans le menu radio, et donc elles sont groupées.

Le nom des menus radio est : <préfixe> <astérisque (ou pas)> <nom de la zone>. Par exemple:

- (sans préfixe)
  - `Cross kobuleti - EASY`
  - `Kutaisi`
  - `* Cross kobuleti - MEDIUM`
- (avec le préfixe "Kobuleti - " et en renommant les zones)
  - `Kobuleti - EASY`
  - `Kobuleti - * MEDIUM`
  - `Kutaisi`

Pour définir un préfixe, on peut utiliser la fonction `setRadioMenuPrefix` ; exemple:

```lua
:setRadioMenuPrefix("Kobuleti - ")
```

**Groupes de menus radio**

On peut grouper les combat zones dans le menu radio. Pour ce faire, il faut utiliser la fonction `setRadioGroupName` ; exemple:

```lua
:setRadioGroupName("Batumi")
```

Toutes les combat zones qui partagent le même groupe radio seront reprises dans un sous-menu nommé comme le groupe.

#### Informations sur les unités

Si on souhaite que le message d'informations de la zone ne comprenne pas la liste des unités (ou juste le résumé, si la zone n'est pas en mode entrainement), on peut utiliser:

```lua
:setShowUnitsList(false)
```

#### Informations sur la position et la météo de la zone

Si on souhaite que le message d'informations de la zone ne comprenne pas la position de la zone ni la météo sur place, on peut utiliser:

```lua
:setShowZonePositionInfo(false)
```

#### Surveillance de la destruction des unités

Les unités concernées par une Combat Zone sont en permanence surveillées. En pratique, toutes les quelques secondes, on lance une vérification de l'état de la zone et des unités.

On vérifie l'état des unités IA spawnées dans le cadre de la zone ; si elles sont toutes détruites la zone est considérée comme terminée, et elle est automatiquement désactivée après un message explicatif

Par défaut, si toutes les unités ennemies de la zone sont détruites, on affiche un message et on la désactive. Si on souhaite désactiver ça, on peut utiliser:

```lua
:setCompletable(false)
```

#### Enchainement des combat zones

Il est possible de paramétrer une chaine de combat zones qui vont s'activer automatiquement l'une après l'autre, la suivante s'activant dès que la précédente est terminée.

Pour ça, on utilise les fonctions `addChainedCombatZone` et (optionnellement) `setChainedCombatZonesDelay`.

Si on configure plusieurs zones "suivantes" pour une zone donnée, celle qui sera effectivement activée est choisie au hasard.

Par exemple, pour obtenir une progression comme:
- Zone 1
- Zone 2
  - Zone 2.1
  - Zone 2.2
- Zone 3

Il faut faire comme suit:

```lua
local zone1 = 
  VeafCombatZone:new()
  :setMissionEditorZoneName("zone1")
  :addChainedCombatZone("zone2")
local zone2 = ...
  VeafCombatZone:new()
  :setMissionEditorZoneName("zone2")
  :addChainedCombatZone("zone2_1")
  :addChainedCombatZone("zone2_2")
  :setChainedCombatZonesDelay(20) -- attendre 20 secondes avant de lancer la zone suivante
local zone2_1 = ...
  VeafCombatZone:new()
  :setMissionEditorZoneName("zone2_1")
  :addChainedCombatZone("zone3")
local zone2_2 = ...
  VeafCombatZone:new()
  :setMissionEditorZoneName("zone2_2")
  :addChainedCombatZone("zone3")
local zone3 = ...
  VeafCombatZone:new()
  :setMissionEditorZoneName("zone3")
  -- pas de "chained combat zone"
```






















### Options de personnalisation

#### Messages et callbacks

Pour chaque zone, il est possible de choisir les messages émis par le système à chaque évènement, et même de spécifier une fonction qui sera appelée quand l'évènement surviendra.

Dans la mesure du possible, les messages sont envoyés aux groupes de joueurs concernés.

Les évènements sont:

- START : démarrage de la zone, au début de la mission (`:setMessageStart()`, `:setOnStart()`)
- WAIT_FOR_HUMANS : le premier joueur est dans la zone, on attend un certain temps les autres (`:setMessageWaitForHumans()`, `:setOnWaitForHumans()`)
- WAIT_TO_DEPLOY : on attend un certain temps avant de déployer une vague (`:setMessageWaitToDeploy()`, `:setOnWaitToDeploy()`)
- DEPLOY : déploiement d'une vague (`:setMessageDeploy()`, `setMessageDeployPlayers()`, `:setOnDeploy()`)
- DESTROYED : une vague a été détruite (`:setMessageDestroyed()`, `:setOnDestroyed()`)
- WON : la zone a été gagnée, plus de vagues IA (`:setMessageWon()`, `:setOnWon()`)
- LOST : la zone a été perdue, plus de joueurs (`:setMessageLost()`, `:setOnLost()`)
- STOP : arrêt de la zone, si `:stop()` est appelée (`:setMessageStop()`, `:setOnStop()`)
- OUTSIDE_OF_ZONE : un joueur est sorti de la zone, on le prévient avant de le détruire (`:setMessageOutsideOfZone()`, `:setOnOutsideOfZone()`)

<u>Exemples:</u>

```lua
-- message when the zone is activated
zone:setMessageStart("%s est active")

-- event when the zone is activated
zone:setOnStart(eventExporter.onStart)

-- message when the zone is waiting for more players
zone:setMessageWaitForHumans("%s: attente d'autres joueurs pendant %s secondes")

-- event when the zone is waiting for more players
zone:setOnWaitForHumans(eventExporter.onWaitForHumans)

-- message when a wave will be triggered
zone:setMessageWaitToDeploy("%s: déploiement de la prochaine vague dans %s secondes")

-- event when a wave will be triggered
zone:setOnWaitToDeploy(eventExporter.onWaitToDeploy)

-- message when a wave is triggered
zone:setMessageDeploy("%s déploie la vague numéro %s")

-- message to players when a wave is triggered
zone:setMessageDeployPlayers("Vague numéro %s déployée, %s") -- the second parameter is a BRA or "MERGED"

-- event when a wave is triggered
zone:setOnDeploy(eventExporter.onDeploy)

-- message when a wave is destroyed
zone:setMessageDestroyed("%s: la vague %s a été détruite")

-- event when a wave is destroyed
zone:setOnDestroyed(eventExporter.onDestroyed)

-- message when all waves are finished (won)
zone:setMessageWon("%s: c'est gagné (plus d'ennemi) !")

-- event when all waves are finished (won)
zone:setOnWon(eventExporter.onWon)

-- message when all players are dead (lost)
zone:setMessageLost("%s: c'est perdu (joueur mort ou sorti) !")

-- event when all players are dead (lost)
zone:setOnLost(eventExporter.onLost)

-- message when the zone is deactivated
zone:setMessageStop("%s n'est plus active")

-- event when the zone is deactivated
zone:setOnStop(eventExporter.onStop)

-- message when a player is outside of zone
zone:setMessageOutsideOfZone("%s - vous êtes en dehors de la zone depuis %s secondes; retournez-y, ou vous serez détruit après %s secondes.")

-- event when a player is outside of zone
zone:setOnOutsideOfZone(eventExporter.onOutsideOfZone)
```

#### Détermination de l'état des groupes et vagues

Il est possible de paramétrer AirWaves pour que le module appelle vos propres fonctions au moment où il se demande si une vague ou un group est encore opérationnel.

Pour cela, deux méthodes existent.

```lua
---the function that decides if a wave is dead or not (as a set of groups and units)
zone:setIsEnemyWaveDeadCallback(callback)
```

La fonction que vous spécifierez en paramètre sera appelée pour déterminer si une vague est considérée comme détruite ou non. Elle prend trois paramètres:
- la zone (l'objet)
- le numéro de la vague courante
- la liste des groupes qui ont été spawnés (leurs noms)

Et elle doit renvoyer `true` si la vague doit être considérée comme hors de combat.

```lua
---the function that decides if a group is dead or not (individually)
zone:setIsEnemyGroupDeadCallback(callback)
```

La fonction que vous spécifierez en paramètre sera appelée pour déterminer si un groupe est considéré comme hors de combat ou non. Elle prend trois paramètres:
- la zone (l'objet)
- le numéro de la vague courante
- un groupe DCS qui a été spawné (une table DCS)

Et elle doit renvoyer `true` si le groupe doit être considéré comme détruit.

Par défaut (si on n'a pas spécifié de callbacks particuliers), on vérifie tous les groupes d'une vague (ce comportement est surchargé par `:setIsEnemyWaveDeadCallback()`), et pour chaque groupe on regarde le pourcentage de points de vie de chacune de ses unités (ce comportement est surchargé par `:setIsEnemyGroupDeadCallback()`). 

S'il est inférieur à une certaine valeur (qu'on peut régler avec `:setMinimumLifeForAiInPercent()`, par défaut 10%), on détruit l'unité en la despawnant (ce comportement est surchargé par `:setHandleCrippledEnemyUnitCallback()`).

#### Gestion des unités considérées comme hors-de-combat

La méthode par défaut (que vous pouvez remplacer avec `:setIsEnemyGroupDeadCallback()`) pour vérifier l'état d'un groupe dans une vague compare son niveau de vie avec un minimum vital qui est déclaré comme une constante (`veafAirWaves.MINIMUM_LIFE_FOR_AI_IN_PERCENT = 10`).

Quand elle détermine qu'une unité doit être détruite, elle appelle une fonction qui se charge de retirer l'unité du groupe. Par défaut, cette fonction déspawn simplement l'unité.

Vous pouvez la remplacer par une de vos fonctions qui fera ce que vous voulez avec cette unité (la faire exploser, c'est joli), en utilisant la fonction suivante:

```lua
--- the function that handles crippled enemy units
zone:setHandleCrippledEnemyUnitCallback(callback)
```

Elle prend trois paramètres:
- la zone (l'objet)
- le numéro de la vague courante
- une unité DCS qui a été spawné (une table DCS)

## Dernière étape

Une fois configurée, la zone doit être lancée. 

Selon le choix qu'on a fait (variable locale ou désignation chaînée), on appelle la méthode `:start()`.

<u>Exemple, désignation chaînée:</u>

```lua
AirWaveZone:new()
    :setName("Minevody")
    :setTriggerZone("Minevody")
    :start()
```

<u>Exemple, variable locale:</u>

```lua
local zone = AirWaveZone:new()
zone:setName("Minevody")
zone:setTriggerZone("Minevody")
zone:start()
```

Si vous souhaitez démarrer la zone dans un trigger, vous pouvez utiliser la méthode `get()` du module pour retrouver l'instance correspondante.

```lua
veafAirWaves.get("Minevody"):start()
```

Bien sûr, il est aussi possible d'appeler d'autres méthodes, telles que `stop()`

```lua
veafAirWaves.get("Minevody"):stop()
```

# Exemples complets

Voici un exemple de configuration fonctionnel et complet (toutes les fonctions disponibles sont présentes)

```lua
    -- example zone 01 (can easily be copy/pasted, nothing to set in the editor except player slots and if desired trigger zones)
    AirWaveZone:new()

    -- technical name (AirWave instance name)
    :setName("Zone 01")

    -- description for the messages
    :setDescription("Zone 01")

    -- coalitions of the players (only human units from these coalitions will be monitored)
    :addPlayerCoalition(coalition.side.BLUE)

    -- trigger zone name (if set, we'll use a DCS trigger zone)
    --:setTriggerZone("airWave_HARD")

    -- center (point in the center of the circle, when not using a DCS trigger zone) - can be set with coordinates either in LL or MGRS
    :setZoneCenterFromCoordinates("U37TFH2882") -- U=UTM (MGRS); 37T=grid number; CL=square; 52000=latitude; 97000=longitude

    -- radius (size of the circle, when not using a zone) - in meters
    :setZoneRadius(90000) -- 50 nm

    -- draw the zone on screen
    :setDrawZone(true)

    -- default position for respawns (im meters, lat/lon, relative to the zone center)
    :setRespawnDefaultOffset(0, 0)

    -- radius of the waves groups spawn
    :setRespawnRadius(0)

    -- delay in seconds between the first human in zone and the actual activation of the zone
    :setDelayBeforeActivation(15)

    -- default delay in seconds between waves of enemy planes
    --:setDelayBetweenWaves(60)

    ---adds a wave of enemy planes
    ---parameters are very flexible: they can be:
    --- a table containing the following fields:
    ---     - groups a list of groups or VEAF commands; VEAF commands can be prefixed with [lat, lon], specifying the location of their spawn relative to the center of the zone; default value is set with "setRespawnDefaultOffset"
    ---     - number how many of these groups will actually be spawned (can be multiple times the same group!); it can be a "randomizable number", e.g., "2-6" for "between 2 and 6"
    ---     - bias shifts the random generator to the right of the list; it can be a "randomizable number" too
    ---     - delay the delay between this wave and the next one - if negative, then the next wave is spawned instantaneously (no waiting for this wave to be completed); it can be a "randomizable number" too
    --- or a list of strings (the groups or VEAF commands)
    --- or almost anything in between; we'll take a string as if it were a table containing one string, anywhere
    --- examples:
    ---   :addWave("group1")
    ---   :addWave("group1", "group2")
    ---   :addWave({"group1", "group2"})
    ---   :addWave({ groups={"group1", "group2"}, number = 2})
    ---   :addWave({ groups="group1", number = 2})
    :addWave({ groups = "-cap easy x1, hdg 180, dist 30", delay = -1 })                    -- an easy single fighter cap
    :addWave({ groups = "-sa9, hdg 180, dist 30", number = "1-3", delay = "15-30" })       -- and simultaneously, between 1 and 3 SA9 groups 
    :addWave({ groups = "-cap normal x1, hdg 180, dist 30" , number = "1-2", delay = -1 }) -- a normal single or two-ship fighter cap after 15-30 seconds
    :addWave({ groups = "-sa8, hdg 180, dist 30", number = "1-3", delay = "15-30" })       -- and simultaneously, between 1 and 3 SA8 groups 
    :addWave({ groups = "-cap hard x1, hdg 180, dist 30" , number = "2-3", delay = -1 })   -- a hard 2 to 3 fighters cap after 15-30 seconds
    :addWave({ groups = "-sa15, hdg 180, dist 30", number = "1-3" })                       -- and simultaneously, between 1 and 3 SA15 groups 

    -- players in the zone will only be detected above this altitude (in feet)
    :setMaximumAltitudeInFeet(40000) -- hard ceiling

    -- players in the zone will only be detected below this altitude (in feet)
    :setMinimumAltitudeInFeet(1500) -- hard floor

    -- message when the zone is activated
    :setMessageStart("%s est maintenant fonctionnelle")

    -- event when the zone is activated
    --:setOnStart(callbackFunction)

    -- message when the zone is waiting for more players
    :setMessageWaitForHumans("%s: attente d'autres joueurs pendant %s secondes")

    -- event when the zone is waiting for more players
    --:setOnWaitForHumans(callbackFunction)

    -- message when a wave will be triggered
    :setMessageWaitToDeploy("%s: déploiement de la prochaine vague dans %s secondes")

    -- event when a wave will be triggered
    --:setOnWaitToDeploy(callbackFunction)

    -- message when a wave is triggered
    :setMessageDeploy("%s déploie la vague numéro %s")

    -- event when a wave is triggered
    :setOnDeploy(function ()
        trigger.action.setUserFlag("monBeauDrapeau", 1)
    end)

    -- message when a wave is destroyed
    :setMessageDestroyed("%s: la vague %s a été détruite")

    -- event when a wave is destroyed
    --:setOnDestroy(callbackFunction)

    -- message when all waves are finished (won)
    :setMessageWon("%s: c'est gagné (plus d'ennemi) !")

    -- event when all waves are finished (won)
    --:setOnWon(callbackFunction)

    -- message when all players are dead (lost)
    :setMessageLost("%s: c'est perdu (joueur mort ou sorti) !")

    -- event when all players are dead (lost)
    --:setOnLost(callbackFunction)

    -- message when the zone is deactivated
    :setMessageStop("%s n'est plus active")

    -- event when the zone is deactivated
    --:setOnStop(callbackFunction)
 
    -- the function that handles crippled enemy units
    :setHandleCrippledEnemyUnitCallback(handleCrippledEnemyUnit)

    :setMinimumLifeForAiInPercent(50)

    ---the function that decides if a group is dead or not (individually)
    --:setIsEnemyGroupDeadCallback(isEnemyGroupDead)

    -- start the zone
    :start()
```

Voici un autre exemple, qui recopie une zone existante (`mist.utils.deepCopy`) au lieu de l'initialiser avec `new()` et qui reconfigure les points de la copie qui seront différents (pratique pour faire plusieurs zones similaires):

```lua
  mist.utils.deepCopy(veafAirWaves.get("Zone 01"))
  :setName("Zone 03")
  :setDescription("Zone 03 - FOX1 fighters")
  :setZoneCenterFromCoordinates("U37TCH2163")
  :start()
```

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

[airwave_zone_example_01]: ../images/airwave_zone_example_01.png
[airwave_zone_example_02]: ../images/airwave_zone_example_02.png
[airwave_zone_example_03]: ../images/airwave_zone_example_03.png