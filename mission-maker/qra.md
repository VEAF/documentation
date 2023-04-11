---
description: Mettre en place une QRA
---

-----------------------------

Navigation: [page "Mission Maker" du site de documentation VEAF](./index.md)

-----------------------------

🚧 **TRAVAUX EN COURS** 🚧

La documentation est en train d'être retravaillée, morceau par morceau. 
En attendant, vous pouvez consulter l'[ancienne documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

# Table des matières

- Introduction - [ici](#introduction)
- Principes du module QRA - [ici](#principes)
- Mise en place dans l'éditeur DCS - [ici](#mise-en-place-dans-léditeur-dcs)
- Où et comment configurer - [ici](#comment-configurer-une-qra)
- Structure d'une déclaration de QRA - [ici](#structure-dune-déclaration-de-qra)
- Options Avancées - [ici](#options-avancées)

## Introduction

Une QRA (Quick Reaction Alert)[https://fr.wikipedia.org/wiki/Quick_Reaction_Alert] est un vol d'appareils de chasse prêt à décoller pour intercepter tout appareil ou missile de croisière menaçant une zone (typiquement une base aérienne, ou une zone sensible).

Grâce à ce module, vous pourrez facilement définir une ou plusieurs zones, circulaires ou polygonales, qui déclencheront le décollage d'un ou plusieurs groupes d'interception si elles sont violées par un pilote humain.

Le but de ce qui suit est de fournir toutes les informations nécessaires pour configurer une QRA (Quick Reaction Alert) dans une mission utilisant les scripts VEAF.

(Veuillez lire [Mission Maker](./index.md)).

## Principes

Une zone de QRA (que nous appelerons QRA à partir d'ici) est constituée de :
- une zone, qui peut être définie par : 
    - une trigger zone DCS placée dans l'éditeur de mission (circulaire ou quadpoint)
    - une zone circulaire avec un centre (défini par des coordonnées en longitude/latitude ou MGRS) et un rayon (en mètres)
- un ou des groupes de réaction :
    - placés dans l'éditeur de DCS, en activation retardée
    - définis par des commandes VEAF, qui se déclenchent au moment du spawn
- des paramètres

La zone a un état qui correspond à son activité :
- tout d'abord elle est prête, et attend qu'on y pénètre
- plus elle déploie ses groupes d'alerte, et devient active pendant toute la durée de leur vol
- ensuite, si ils sont détruits ou déroutés, elle devient inactive jusqu'à ce que tous les appareils humains qui s'y trouvent en sortent
- et enfin elle redevient prête

Ceci est le schéma standard. En pratique, des dizaines d'options (décrites plus loin) vous permettent d'adapter le comportement des QRA comme vous les souhaitez.

Les QRA peuvent également être liées à une base aérienne et subir des contraintes logistiques (nombre de respawn réduit, ou incapacitation en cas de destruction de la base, par exemple)

## Mise en place dans l'éditeur DCS

Tout d'abord, le groupe QRA doit exister au sein de la mission. Tout type d'aéronef (démarrage en vol ou au sol) peut être utilisé et leur charge utile/plan de vol existant sera conservé.

Cette étape est similaire à toute autre mission, placez le groupe de votre choix, configurez-le, **enclenchez l'activation différée** et donnez-lui un nom facilement reconnaissable car il sera utilisé par la suite.


De plus, vous devrez créer une zone de déclenchement. Avec un nom reconnaissable (qui peut être identique à celui du groupe d'aéronefs) que nous utiliserons aussi par la suite.

Ceci est simplement pour avoir un point de référence pour la zone de déploiement du QRA, son rayon dans DCS n'a pas d'importance et sera défini ultérieurement.

## Comment configurer une QRA

Concernant la partie script, tout commence dans le fichier de configuration de la mission `missionConfig.lua`, qui est situé dans le répertoire `src/scripts` de votre mission.

Ce dernier est un fragment de code source (LUA)[https://fr.wikipedia.org/wiki/Lua] qui permet de choisir la manière dont les scripts VEAF sont activés et configurés. Nous vous conseillons d'utiliser Notepad++ ou Visual Studio Code pour l'éditer.

Par défaut, si vous avez utilisé le [convertisseur de mission existante][VEAF-mission-converter-repository] pour préparer votre dossier de mission VEAF, il contient déjà un modèle de configuration QRA que vous pourrez trouver en cherchant "-- initialize QRA".

Le principe de configuration est simple.

Tout d'abord on crée un "objet" de type *VeafQRA* en appelant `VeafQRA:new()`. Cet appel renvoie une instance de *VeafQRA*, qu'on peut stocker dans une variable (`local maQra = VeafQRA:new()`) ou utiliser tout de suite avec une (désignation chaînée)[https://fr.wikipedia.org/wiki/D%C3%A9signation_cha%C3%AEn%C3%A9e] (en enchainant les appels aux méthodes de configuration qui renvoient toutes la même instance de *VeafQRA*).

Exemple de chainage:

```lua
VeafQRA:new()
    :setName("QRA_Minevody")
    :setTriggerZone("QRA_Minevody")
    :setZoneRadius(106680)
```

L'avantage de cette méthode est sa simplicité.

Exemple d'utilisation d'une variable:

```lua
local qraMinevody = VeafQRA:new()
qraMinevody:setName("QRA_Minevody")
qraMinevody:setTriggerZone("QRA_Minevody")
qraMinevody:setZoneRadius(106680)
```

L'avantage de la seconde méthode est qu'on peut, plus loin dans le fichier `missionConfig.lua`, utiliser une référence à la variable qu'on a définie pour accéder à l'instance de VeafQRA (par exemple, dans la définition d'un alias, d'une commande "mission maker", ou dans un menu radio).

Vous pouvez utiliser l'option suivante pour activer (false) ou désactiver (true) l'envoi de messages par la QRA:

```lua
VeafQRA.ToggleAllSilence(*false or true*) --Activera tous les messages QRA visibles par le joueur si l'argument est "true" et désactivera tous les messages QRA si l'argument est "false".
```

## Structure d'une déclaration de QRA

### Options Générales

```lua
VeafQRA.new()
```

Création d'une nouvelle instance de QRA: une instance définit une zone et son comportement; exemple: la QRA de Minevody.

```lua
:setName("QRA_Minevody") -- Définit le nom de la QRA pour les scripts (principalement à des fins d'affichage)
```

Définition du nom technique de la QRA: ce nom sert à retrouver la QRA avec la commande `veafQraManager.get()`; exemple: `veafQraManager.get("QRA_Minevody")`

```lua
:setTriggerZone("QRA_Minevody_triggerzone") -- Indique aux scripts quelle zone de déclenchement est utilisée référence de la zone de déploiement de la QRA.
```

Définition de la zone via une trigger zone DCS

```lua
:setZoneCenterFromCoordinates("U37TCL5297") -- U=UTM (MGRS); 37T=grid number; CL=square; 52000=latitude; 97000=longitude
:setZoneRadius(50000) -- Définit le rayon de la zone de déploiement (en mètres)
```

Utilisation de coordonnées (MGRS ou Latitude/Longitude) et d'un rayon pour définir la zone

```lua
:setCoalition("red") -- Définit la coalition à laquelle la QRA appartiendra
```

Définition de la coalition qui gère la QRA (en général, ce sont les ennemis des joueurs, souvent les rouges).

Valeurs acceptées:
 - "red"
 - "blue"
 - coalition.side.RED
 - coalition.side.BLUE
 - coalition.side.NEUTRAL

```lua
:addEnnemyCoalition("blue") -- Ajoute une coalition dont les avions déclencheront le déploiement de la QRA.
```

Les joueurs qui font partie de cette ou ces coalitions seront surveillés, et la QRA sera déclenchée s'ils entrent dans la zone.

On accepte les mêmes valeurs que pour `setCoalition()`

```lua
:setReactOnHelicopters() --Définit si la QRA réagit aux hélicoptères qui entrent dans la zone
```

```lua
:setSilent() --Désactive les messages de cette QRA uniquement. VeafQRA.ToggleAllSilence doit être "false" pour que cela ait un effet.
```

```lua
:setDelayBeforeRearming(*value*) --Délai entre la mort d'une QRA et le réarmement de nouveau avions
```

```lua
:setNoNeedToLeaveZoneBeforeRearming() --La QRA réapparaîtra même si des joueurs sont toujours dans la zone (zone de déploiement) après son réarmement
```

```lua
:setResetWhenLeavingZone() --La QRA sera désactivée (et réarmée immédiatement) lorsque tous les joueurs quittent la zone. Sinon, la QRA patrouillera jusqu'à ce qu'elle retourne à la base après activation, moment où elle sera désactivée lors de l'atterrissage et réaarmée immédiatement.
```

```lua
:setDelayBeforeActivating(*value*) --Délai d'activation entre l'entrée d'unités dans la zone de la QRA et le déploiement de la QRA
```

```lua
:setMinimumAltitudeInFeet(*value*) --Défini l'altitude minimale pour la détection d'un appareil et donc le déploiement de la QRA
```

```lua
:setMaximumAltitudeInFeet(*value*) --Défini l'altitude maximale pour la détection d'un appareil et donc le déploiement de la QRA
```

```lua
:addGroup("qra_Mig29") -- Vous pouvez utiliser ceci pour ajouter un ou plusieurs groupes d'avions à la QRA (syntaxe #1)
```

```lua
:addGroup({ "qra_Mig29" }) -- Vous pouvez utiliser ceci pour ajouter un ou plusieurs groupes d'avions à la QRA (syntaxe #2)
```

#### Options pour le comportement d'une QRA Multi-Groupe

Concernant l'utilisation de multiples groupes dans une QRA : 

```lua
    :addRandomGroup(*groups*, *number*, *bias*) --Ajoute aléatoirement *number* groupes pris parmis la liste des *groups* (liste avec le format suivant : {"*NameOfQRA_1*",                                                 --"*NameOfQRA_2*", ...} ) avec un biais vers l'élément numéro *bias*

    :setGroupsToDeployByEnemyQuantity(*enemyNb*, *groupsToDeploy*) --Lorsque *enemyNb* appareils sont détéctés dans la zone de la QRA, ceci fait que les groupes de la liste                                                                    --*groupsToDeploy* (liste avec le format suivant : {"*NameOfQRA_1*", "*NameOfQRA_2*", ...} ) seront                                                                          --déployés.

    :setRandomGroupsToDeployByEnemyQuantity(*enemyNb*, *groups*, *number*, *bias*) --Lorsque *enemyNb* appareils sont détéctés dans la zone de la QRA, ceci fait que                                                                                            --aléatoirement, *number* des groupes parmis la liste *groups* (liste avec le format                                                                                        --suivant : {"*NameOfQRA_1*", "*NameOfQRA_2*", ...} ) seront déployés.
                                                                                   --Avec un biais vers l'élément numéro *bias* de la liste.
```

#### Options Logistique

De plus, il est possible de contrôler étroitement la logistique de la QRA. Vous pouvez :


- Spécifier combien de déclenchements la QRA peut effectuer au départ (1 déclenchement = tous les groupes d'avion qui apparaîssent au lancement d'une QRA, les avions ne sont pas comptés individuellement ici)
  (vous pouvez utiliser cela pour rendre la QRA progressivement plus difficile à épuiser en commençant avec peu de déclenchements)

```lua
    :setQRAcount(\*QRAcount\*) --Supérieur ou égal à -1 : Nombre actuel d'ensembles d'avions disponibles pour le déploiement. Par défaut, cela est réglé sur -1, ce qui                                    --signifie qu'un nombre infini de groupes est disponible, aucune logistique ne prend place n'est effectué.
```

-> C'est votre "master arm" pour le reste de ces options.


- Spécifier le nombre de "places de parking" disponibles pour une QRA, qui ne peuvent pas être dépassées et, vu qu'elles sont limitées, peuvent conduire à l'épuisement de la QRA avec le temps

```lua
    :setQRAmaxCount(\*maxQRAcount\*) --Supérieur ou égal à -1 : Nombre maximal d'ensembles d'avions déployables à tout moment pour la QRA. Par défaut, cela est réglé sur                                        -- -1, ce qui signifie qu'un nombre infini d'avions peut être accumulé pour le déploiement.
```

-> Exemple : Une QRA a 2 places sur 6 de remplies et prêtes pour le déploiement, 6 correspond au maxQRAcount, 2 au QRAcount actuel.


- Spécifier le nombre d'avions en "stockage" qui peuvent être expédiés aux "places de parking" mentionnées précédemment

```lua
    :setQRAmaxResupplyCount(\*maxResupplyCount\*) --Supérieur ou égal à -1 : Nombre total de groupes d'avions pouvant être provisionnés à la QRA. Par défaut, cela est réglé sur -1, ce qui signifie qu'une quantité infinie de stock est disponible. 0 signifie qu'aucun stock n'est disponible, aucun réapprovisionnement ne se produira, c'est votre "master arm" pour les réapprovisionnements. 
```

-> Prenons l'exemple précédent : nous avons besoin de 4 groupes (4 "places de parking" vides) mais nous n'en avons que 3 en stock pour réapprovisionner la QRA, 3 correspond au QRAmaxResupplyCount.
    

- Spécifier un seuil pour le nombre d'ensembles d'avions disponibles, en dessous duquel un réapprovisionnement est lancé :

```lua
    :setQRAminCountforResupply(\*minCountforResupply\*) --Égal à -1 ou supérieur à 0 : Nombre d'ensembles d'avions que la QRA doit avoir à tous temps, sinon un réapprovisionnement sera lancé. Par défaut, cela est réglé sur -1, ce qui signifie qu'un réapprovisionnement sera lancé dès qu'un ensemble d'avions est perdu.
```

-> Prenons l'exemple précédent : le nombre minimum d'ensembles d'avions déployables que nous souhaitons avoir en tout temps pour notre QRA est de 1, mais nous en avons 2, donc aucun réapprovisionnement ne se produira pour le moment. 1 correspond au minCountforResupply.
    

- Spécifier combien d'avions sont expédiés avec chaque réapprovionnements :

```lua
    :setResupplyAmount(\*resupplyAmount\*) --Supérieur ou égal à 1 : Nombre de groupes d'avions qui seront fournis à la QRA lorsqu'un réapprovisionnement se produit. Par défaut, cela est égal à 1. 
```
    
-> Prenons l'exemple précédent : nous venons de perdre nos deux znsembles, ce qui signifie que nous n'en avons plus, cela déclenchera un réapprovisionnement, qui apportera soit le nombre désiré d'ensembles soit le nombre d'avions que nous avons en stock si cette quantité est inférieure. Le réapprovisionnement sera également limité par le nombre maximum d'ensembles que nous pouvons avoir en même temps.
    

- Spécifier combien de temps prend un réapprovisionnement :

```lua
    :setQRAresupplyDelay(\*resupplyDelay\*) --Supérieur ou égal à 0 : Temps nécessaire pour effectuer un réapprovisionnement
```

**Plusieurs choses à noter ici :**

- Seul un réapprovisionnement peut avoir lieu à la fois, ils peuvent être programmés à chaque occasion possible (à chaque fois qu'un groupe QRA est perdu par exemple) mais n'auront lieu qu'un à la fois.
        
- Les groupes QRA qui viennent d'être provisionnés devront être réarmés (voir le délai et les contraintes associés [ici](#general-options))

#### Options pour le lien avec un aéroport / une base

Enfin, il est possible de lier les mécanismes de déploiement et de logistique de la QRA à la possesion (ou non) d'une base aérienne, d'un FARP, d'un navire ou d'un bâtiment par la coalition de la dite QRA :

```lua
    :setAirportLink("*airbase_name*") --Nom de l'unité / de la base aérienne : la QRA sera liée à cette base et cessera de fonctionner si la base est perdue (il peut s'agir d'un FARP (utilisez le nom de l'unité du FARP), d'un navire (utilisez le nom de l'unité du navire), d'un aérodrome ou d'un bâtiment (plateformes pétrolières, etc.))
```
  
**Non fonctionnel à ce jour :**

```lua
    :setAirportMinLifePercent(*value*) --Comprise entre 0 et 1 : pourcentage minimum de vie de l'aéroport lié pour que le QRA fonctionne. Les aéroports (pistes) et les navires ne devraient perdre de la vie que lorsqu'ils sont bombardés, cela nécessite des tests manuels pour savoir ce qui fonctionne le mieux.
```

**Plusieurs choses à noter ici :**

- La partie logistique est interrompue lorsque l'aérodrome est perdu.

- Une QRA qui est activée après la prise de son aérodrome par sa coalition devra être réarmée (voir le délai et les contraintes associés [ici](#general-options))

#### Dernière étape

Après avoir enchaîné ces options, vous pouvez ajouter cela :

```lua
    :start()
```

Comme son nom l'indique, cela démarre la QRA. Sinon, elle restera inactive.


Ou si vous souhaitez la démarrer ultérieurement dans l'un de vos scripts :

```lua
    if QRA_*NameOfQRA* then QRA_*NameOfQRA*:start() end
```

Vous pouvez également utiliser pour faire l'action inverse :

```lua
    if QRA_*NameOfQRA* then QRA_*NameOfQRA*:stop() end --utilisez ceci si vous souhaitez arrêter la QRA à un moment donné (dans un déclencheur, etc.). Elle peut être redémarrée en utilisant la méthode précédemment mentionnée.
```

Cela rend inactif la QRA (stop toute vérification d'aéroport, toutes les opérations de logistique, etc.).

### Options Avancées

En plus de ce qui a été dit précedemment, il est possible de modifier certaines options de "bas niveau".

#### Messages Joueur

Cette section concerne la modification des messages affichés aux joueurs des coalitions qui deuvent déclencher la QRA selon ses états :

```lua
    :setMessageStart("*value*") --Lorsque :start() est utilisé

    :setMessageDeploy("*value*") --Lorsque la QRA est déployée

    :setMessageDestroyed("*value*") --Lorsque la QRA est détruite

    :setMessageReady("*value*") --Lorsque la QRA est de nouveau prête au déploiement

    :setMessageOut("*value*") --Lorque la QRA est à court d'ensembles d'avions

    :setMessageResupplied("*value*") --Lorsque la QRA possède de nouveau des avions

    :setMessageAirbaseDown("*value*") --Lorsque la QRA perd sa base

    :setMessageAirbaseUp("*value*") --Lorsque la QRA regagne sa base

    :setMessageStop("*value*") --Lorsque :stop() est utilisé
```

## Contacts

Si vous avez besoin d'aide, ou si vous voulez suggérer quelque chose, vous pouvez :

* contacter [Rex][RexAttaque on Github] sur GitHub
* contacter [Zip][Zip on Github] sur GitHub
* aller consulter le [site de la VEAF][VEAF website]
* poster sur le [forum de la VEAF][VEAF forum]
* rejoindre le [Discord de la VEAF][VEAF Discord]


[Badge-Discord]: https://img.shields.io/discord/471061487662792715?label=VEAF%20Discord&style=for-the-badge
[VEAF-logo]: ../images/logo.png?raw=true
[VEAF Discord]: https://www.veaf.org/discord
[Zip on Github]: https://github.com/davidp57
[VEAF website]: https://www.veaf.org
[VEAF forum]: https://www.veaf.org/forum

[VEAF-Mission-Creation-Tools-repository]: https://github.com/VEAF/VEAF-Mission-Creation-Tools
[VEAF-mission-converter-repository]:https://github.com/VEAF/VEAF-mission-converter
[VEAF-demo-mission-repository]: https://github.com/VEAF/VEAF-Demo-Mission
[VEAF-Open-Training-Mission-repository]: https://github.com/VEAF/VEAF-Open-Training-Mission
[VEAF-Multiplayer-Missions-repository]: https://github.com/VEAF/VEAF-Multiplayer-Missions

[demo-mission-structure]: ../images/demo-mission-structure.png
