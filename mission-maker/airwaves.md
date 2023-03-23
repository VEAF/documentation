---
description: Mettre en place une zone d'entrainement AirWaves
---

-----------------------------

Navigation: [VEAF documentation site - main page](../index.md)

-----------------------------

🚧 **TRAVAUX EN COURS** 🚧

La documentation est en train d'être retravaillée, morceau par morceau. 
En attendant, vous pouvez consulter l'[ancienne documentation](https://github.com/VEAF/VEAF-Mission-Creation-Tools/blob/master/old_documentation/_index.md).

-----------------------------

# Table des matières

TBD

# Introduction

Le module *AirWaves* permet de créer facilement des zones d'entrainement, sur le modèle de la [QRA](qra.md) (en tout cas pour ce qui est des paramètres), dans lesquelles les joueurs font face à des vagues d'ennemis IA successives qu'ils doivent vaincre les unes après les autres.

A la base, les zones *AirWaves* font spawner des groupes aériens, mais il est tout à fait possible de spawner des unités au sol ou des navires. Le principe reste le même: vaincre toutes les vagues les unes après les autres.

En cas d'échec (perte du combat contre les IA), la zone est remise à zéro et les groupes IA restants sont détruits. De cette manière, tout est prêt pour le joueur suivant.

# Principes

Une zone *AirWaves* est constituée de :
- une zone, qui peut être définie par : 
    - une trigger zone DCS placée dans l'éditeur de mission (circulaire ou quadpoint)
    - une zone circulaire avec un centre (défini par des coordonnées en longitude/latitude ou MGRS) et un rayon (en mètres)
- une ou des vagues d'IA composées de :
    - un ou des groupes d'IA :
        - placés dans l'éditeur de DCS, en activation retardée
        - définis par des commandes VEAF, qui se déclenchent au moment du spawn
- des paramètres

La zone a un état qui correspond à son activité :
- tout d'abord elle est prête, et attend qu'on y pénètre ou qu'on y spawn
- plus elle déploie la première vague, et devient active pendant toute la durée de leur vol
- ensuite, si cette vague est détruite ou déroutée, la zone déploie la vague suivante
- si toutes les vagues sont épuisées, la zone est marquée comme *gagnée* et est réinitialisée dès que les joueurs l'ayant déclenchée ont despawné.
- si les joueurs sont tous détruits, la zone est marquée comme *perdue* et est réinitialisée

Ceci est le schéma standard. En pratique, des dizaines d'options (décrites plus loin) vous permettent d'adapter le comportement des zones *AirWaves* comme vous le souhaitez.

# Comment configurer une zone *AirWaves*

Tout commence dans le fichier de configuration de la mission `missionConfig.lua`, qui est situé dans le répertoire `src/scripts` de votre mission.

Ce dernier est un fragment de code source [LUA](https://fr.wikipedia.org/wiki/Lua) qui permet de choisir la manière dont les scripts VEAF sont activés et configurés. Nous vous conseillons d'utiliser Notepad++ ou Visual Studio Code pour l'éditer.

Par défaut, si vous avez utilisé le [convertisseur de mission existante][VEAF-mission-converter-repository] pour préparer votre dossier de mission VEAF, il contient déjà un modèle de configuration QRA que vous pourrez trouver en cherchant `-- initialize AirWaves zones`.

Le principe de configuration est simple.

Tout d'abord on crée un "objet" de type *AirWaveZone* en appelant `AirWaveZone:new()`. Cet appel renvoie une instance de *AirWaveZone*, qu'on peut stocker dans une variable (`local maZone = AirWaveZone:new()`) ou utiliser tout de suite avec une [désignation chaînée](https://fr.wikipedia.org/wiki/D%C3%A9signation_cha%C3%AEn%C3%A9e) (en enchainant les appels aux méthodes de configuration qui renvoient toutes, tour à tour, la même instance de *AirWaveZone*).

Exemple de chainage:

```lua
AirWaveZone:new()
    :setName("Minevody")
    :setTriggerZone("Minevody")
```

L'avantage de cette méthode est sa simplicité.

Exemple d'utilisation d'une variable:

```lua
local zoneMinevody = AirWaveZone:new()
zoneMinevody:setName("Minevody")
zoneMinevody:setTriggerZone("Minevody")
```

L'avantage de la seconde méthode est qu'on peut, plus loin dans le fichier `missionConfig.lua`, utiliser une référence à la variable qu'on a définie pour accéder à l'instance de *AirWaveZone* (par exemple, dans la définition d'un alias, d'une commande "mission maker", ou dans un menu radio).

# Structure d'une déclaration de zone *AirWaves*

## Création de la zone

Création d'une nouvelle instance de *AirWaveZone*: une instance définit une zone et son comportement; exemple: la zone de Minevody.

Utiliser soit une variable locale, soit une désignation chaînée (voir (ce chapitre)[#comment-configurer-une-zone-AirWaves]) pour exploiter cette instance.

```lua
AirWaveZone.new()
```

## Paramètres obligatoires

Définition du nom technique de la QRA: ce nom sert à retrouver la QRA avec la commande `veafQraManager.get()`; exemple: `veafQraManager.get("QRA_Minevody")`

```lua
:setName("Minevody")
```

Description de la zone: c'est ce qui sera repris dans les différents messages

```lua
:setDescription("Zone 01 - FOX1 fighters")
```

Définition de la zone via une trigger zone DCS

On peut choisir de définir la zone à partir d'une trigger zone, ou avec un centre et un rayon (voir plus bas); c'est l'un ou l'autre.

```lua
:setTriggerZone("airWave_HARD")
```

Utilisation de coordonnées (MGRS ou Latitude/Longitude) et d'un rayon pour définir la zone.

On peut choisir de définir la zone avec un centre et un rayon, ou à partir d'une trigger zone (voir plus haut); c'est l'un ou l'autre.

```lua
:setZoneCenterFromCoordinates("U37TCL5297") -- U=UTM (MGRS); 37T=grid number; CL=square; 52000=latitude; 97000=longitude
:setZoneRadius(50000) -- Définit le rayon de la zone (en mètres)
```

Définition de la coalition qui gère la QRA (en général, ce sont les ennemis des joueurs, souvent les rouges).

Valeurs acceptées:
 - "red"
 - "blue"
 - coalition.side.RED
 - coalition.side.BLUE
 - coalition.side.NEUTRAL

```lua
:setCoalition("red") -- Définit la coalition à laquelle la QRA appartiendra
```

Les joueurs qui font partie de cette ou ces coalitions seront surveillés, et la zone sera déclenchée uniquement pour eux.

On accepte les mêmes valeurs que pour `setCoalition()`

```lua
:addPlayerCoalition(coalition.side.BLUE)
```

## Paramètres optionnels

Définition d'un plancher et d'un plafond (en pieds); les joueurs qui pénètrent dans la zone ne sont pas détectés s'ils sont au dessus du plafond ou en dessous du plancher (càd que la zone ne se déclenche pas).

Par défaut, pas de plancher ni de plafond

```lua
:setMaximumAltitudeInFeet(40000) -- hard ceiling
:setMinimumAltitudeInFeet(1500) -- hard floor
```

Dessin de la zone sur la carte F10; par défaut, pas de dessin.

```lua
:setDrawZone(true)
```

## Gestion des vagues

A la base, le but de ce script est d'opposer à la présence de joueurs des vagues d'avions IA qui viennent les affronter.

Il est possible d'utiliser des groupes d'avions placés dans l'éditeur de DCS (en *late activation*), ou des commandes VEAF (voir la [documentation des scripts - wip](../index.md) ou la [documentation de la mission Opentraining][VEAF-Open-Training-Mission-documentation]

Le fait qu'on puisse utiliser des commandes VEAF fait qu'on peut opposer aux joueurs toutes sortes d'ennemis (SAM, manpads, unités blindées, convois, navires); on peut aller jusqu'à détourner l'usage classique de ce script pour déclencher des actions (une combat zone, la mise en déplacement d'un porte-avion).

### Paramètres

Choix de la position de spawn par défaut, relativement au centre de la zone (en mètres, positif vers l'Est et le Sud, négatif vers l'Ouest et le Nord).

Cette position est utilisée quand on ne peut pas déterminer la position de départ du groupe (ça arrive parfois avec DCS, de manière inexpliquée), ou quand le groupe est en fait une commande VEAF et qu'on n'a pas précisé de position dans la commande.

```lua
:setRespawnDefaultOffset(0, -45000) -- 45km north of the zone's center
```

Choix du rayon d'apparition; on peut préciser un cercle (en mètres) autour du point de spawn, dans lequel on choisira aléatoirement le point d'apparition de chaque groupe (ou commande).

Ce paramètre est appliqué __en plus__ de la position de spawn déterminée par le groupe (dans l'éditeur DCS), par le décalage par défaut (*setRespawnDefaultOffset*), ou par les coordonnées dans la commande.

```lua
:setRespawnRadius(10000)
```

Exemple:

```lua
AirWaveZone:new()
    :setName("Minevody")
    :setTriggerZone("Minevody")
    :setRespawnDefaultOffset(0, -45000) -- 45km north of the zone's center
    :setRespawnRadius(10000)
    :addWave("[0, 30000]-cap mig29-fox1", "zone1_su27", "-sa15")
```

Dans ce cas, le groupe `[0, 30000]-cap mig29-fox1` (une commande VEAF) a des coordonnées (décalage à partir du centre de la zone) spécifiées dans la définition de la commande VEAF (entre crochets).

Il va donc spawner à 30km au sud du centre de la zone, qui correspond à la trigger zone *Minevody*.

Mais on ajoute un *respawn radius* de 10km, ce qui fait qu'en pratique il spawnera à une position aléatoire dans un cercle de 10km autour de ce point.

Le groupe `zone1_su27` (un groupe DCS) n'a pas de coordonnées. Si DCS fonctionne correctement, il va spawner à l'emplacement où il a été placé dans l'éditeur.

Mais ici aussi on doit tenir compte du *respawn radius*: il spawnera à une position aléatoire dans un cercle de 10km autour de ce point.

Le dernier groupe `-sa15` (lui aussi une commande VEAF) n'a pas de coordonnées intrinsèques. Il va donc spawner à l'emplacement par défaut, qui est précisé par la commande `setRespawnDefaultOffset` par rapport au centre de la zone (ici, 45km au nord).

Et bien sûr on appliquera le *respawn radius*: il spawnera à une position aléatoire dans un cercle de 10km autour de ce point.

### Choix aléatoire

La définition d'une vague, qu'elle soit composée de groupes DCS, de commandes VEAF ou des deux, se fait en choisissant aléatoirement un nombre déterminé de groupes/commandes dans une liste, en appliquant un biais optionnel.

Tout d'abord, il faut définir une liste de groupes et/ou de commandes VEAF. En lua, les listes sont encadrées par des accolades {}, et les chaines de caractères par des guillemets "".

On utilisera la méthode `:addRandomWave()` qui prend en premier paramètre cette liste, et en second le nombre de groupes/commandes à spawner pour cette vague. Le troisième paramètre (optionnel) est le biais (voir plus bas).

Exemple où on définit 4 groupes dont 2 commandes VEAF et 2 groupes DCS, et où on en spawn 2:

```lua
:addRandomWave({"groupe 1", "-sa15", "groupe 2", "[10000, 15000]-cap mig29, size 2, hdg 180"}, 2)
```

Attention, il est tout à fait possible que les groupes spawnés soient dupliqués (par exemple, 2 fois "groupe 1" dans notre exemple)

Au moment du déploiement, on va aléatoirement choisir un nombre entre 1 (+ le biais) et la taille de la liste (+ le biais), et on va prendre l'élément correspondant dans la liste (ou le dernier si le choix est trop grand) pour le spawner; et ce autant de fois qu'il le faut (2, dans notre exemple).

Le biais permet donc de décaler vers la droite de la liste le choix aléatoire.

Exemple sans biais:

```lua
:addRandomWave({"groupe 1", "groupe 2", "groupe 3", "groupe 4"}, 1)
```

Ici, on choisit aléatoirement un groupe dans la liste

Exemple avec biais:

```lua
:addRandomWave({"groupe 1", "groupe 2", "groupe 3", "groupe 4"}, 1, 1)
```

Ici, on décide délibéremment de ne jamais choisir "groupe 1" ; on choisit donc aléatoirement un groupe dans la liste "groupe 2", "groupe 3", "groupe 4".

Exemple complet:

```lua
AirWaveZone:new()
    :setName("Minevody")
    :setTriggerZone("Minevody")
    :setRespawnDefaultOffset(0, -45000) -- 45km north of the zone's center
    :setRespawnRadius(10000)
    :addWave("-cap mig29-fox1", "-cap su27-fox1", "-cap su33-fox3", 2, -1) -- choose between group 1 and group 2, never pick group 3
    :addWave("-cap mig29-fox1", "-cap su27-fox1", "-cap su33-fox3", 2, 0)  -- choose between group 1, group 2 and group 3
    :addWave("-cap mig29-fox1", "-cap su27-fox1", "-cap su33-fox3", 2, 1)  -- choose between group 2 and group 3, never pick group 1
```

Avec la même ligne ou presque, on définit trois vagues très différentes !

### Choix systématique

On peut aussi définir, pour une vague, un seul groupe (ou une seule commande VEAF), en utilisant la méthode `addWave`.

Exemple:

```lua
:addWave("groupe 1")
```

### Utilisation de commandes

Un groupe dans une vague (dans la liste, entre les accolades) peut donc correspondre au nom d'un groupe d'avions de DCS, défini dans l'éditeur de mission, ou une commande VEAF.

Les commandes VEAF sont définies dans les différents scripts qui composent la bibliothèque VEAF, et peuvent avoir des effets très variés (déclencher une frappe d'artillerie, démarrer le déplacement d'un groupe naval - porte-avion -, lancer une *combat zone*, éclairer une zone, spawner des blindés, des SAM, un convoi, un FARP, une FOB, ...).

Pour faire simple, tout ce que les scripts VEAF reconnaissent dans un marqueur sur la carte F10 peut être utilisé ici.

Le point de spawn calculé (voir le paragraphe plus haut qui explique ce concept) sera en fait le point d'ancrage de la commande (ce sera comme si on avait mis un marqueur ici sur la vue F10). Si la commande n'est pas localisée (ex: déclenchement d'une *combat zone*) ou si elle contient des coordonnées absolues (ex `U37TCL5297`), ce point d'ancrage n'aura aucun effet.

Entre crochets, on peut (optionnellement) spécifier un point de spawn (relatif au centre de la zone), qui modifiera le point d'ancrage.

Exemple, déclenchement d'une cap:

```lua
:addWave("-cap mig29")
```

Exemple, déclenchement d'une cap avec offset par rapport au centre de la zone:

```lua
:addWave("[-15000, 30000]-cap mig29, hdg 135, dist 40")
```

Exemple, spawn d'un SA15 au centre de la zone

```lua
:addWave("[0, 0]-sa15")
```

Exemple, création d'un convoi blindé qui se déplace du centre de la zone vers Kobuleti

```lua
:addWave("[0, 0]-convoy, armor 4, defense 2, dest kobuleti")
```

Exemple, appel d'une frappe d'artillerie antiaérienne

```lua
:addWave("-flak")
```

Exemple, appel d'une frappe d'artillerie sur coordonnées

```lua
:addWave("-shell#U37TGG2164791685")
```

## Options de personnalisation (messages et callbacks)

Pour chaque zone, il est possible de choisir les messages émis par le système à chaque évènement, et même de spécifier une fonction qui sera appelée quand l'évènement surviendra.

Les évènements sont:

- START : démarrage de la zone, au début de la mission (`:setMessageStart()`, `:setOnStart()`)
- DEPLOY : déploiement d'une vague (`:setMessageDeploy()`, `:setOnDeploy()`)
- DESTROYED : une vague a été détruite (`:setMessageDestroyed()`, `:setOnDestroyed()`)
- WON : la zone a été gagnée, plus de vagues IA (`:setMessageWon()`, `:setOnWon()`)
- LOST : la zone a été perdue, plus de joueurs (`:setMessageLost()`, `:setOnLost()`)
- STOP : arrêt de la zone, si `:stop()` est appelée (`:setMessageStop()`, `:setOnStop()`)

Exemples:
```lua
-- message when the zone is activated
zone:setMessageStart("%s est maintenant fonctionnelle")

-- event when the zone is activated
zone:setOnStart(bluejaag.eventExporter.onStart)

-- message when a wave is triggered
zone:setMessageDeploy("%s déploie la vague numéro %s")

-- event when a wave is triggered
zone:setOnDeploy(bluejaag.eventExporter.onDeploy)

-- message when a wave is destroyed
zone:setMessageDestroyed("%szone: la vague %s a été détruite")

-- event when a wave is destroyed
zone:setOnDestroyed(bluejaag.eventExporter.onDestroyed)

-- message when all waves are finished (won)
zone:setMessageWon("%szone: c'est gagné (plus d'ennemi) !")

-- event when all waves are finished (won)
zone:setOnWon(bluejaag.eventExporter.onWon)

-- message when all players are dead (lost)
zone:setMessageLost("%szone: c'est perdu (joueur mort ou sorti) !")

-- event when all players are dead (lost)
zone:setOnLost(bluejaag.eventExporter.onLost)

-- message when the zone is deactivated
zone:setMessageStop("%s n'est plus active")

-- event when the zone is deactivated
zone:setOnStop(bluejaag.eventExporter.onStop)
```

## Lancement de la zone

Une fois configurée, la zone doit être lancée. 

Selon le choix qu'on a fait (variable locale ou désignation chaînée), on appelle la méthode `:start()`.

Exemple, désignation chaînée:

```lua
AirWaveZone:new()
    :setName("Minevody")
    :setTriggerZone("Minevody")
    :start()
```

Exemple, variable locale:

```lua
local zone = AirWaveZone:new()
zone:setName("Minevody")
zone:setTriggerZone("Minevody")
zone:start()
```



























#### Options pour le comportement d'une QRA Multi-Groupe

Concernant l'utilisation de multiples groupes dans une QRA : 

    :addRandomGroup(*groups*, *number*, *bias*) --Ajoute aléatoirement *number* groupes pris parmis la liste des *groups* (liste avec le format suivant : {"*NameOfQRA_1*", "*NameOfQRA_2*", ...} ) avec un biais vers l'élément numéro *bias*

    :setGroupsToDeployByEnemyQuantity(*enemyNb*, *groupsToDeploy*) --Lorsque *enemyNb* appareils sont détéctés dans la zone de la QRA, ceci fait que les groupes de la liste *groupsToDeploy* (liste avec le format suivant : {"*NameOfQRA_1*", "*NameOfQRA_2*", ...} ) seront déployés

    :setRandomGroupsToDeployByEnemyQuantity(*enemyNb*, *groups*, *number*, *bias*) --Lorsque *enemyNb* appareils sont détéctés dans la zone de la QRA, ceci fait que aléatoirement, *number* des groupes parmis la liste *groups* (liste avec le format suivant : {"*NameOfQRA_1*", "*NameOfQRA_2*", ...} ) seront déployés.
                                                                                     Avec un biais vers l'élément numéro *bias* de la liste.

## Mise en place dans l'éditeur DCS

Tout d'abord, le groupe QRA doit exister au sein de la mission. Tout type d'aéronef (démarrage en vol ou au sol) peut être utilisé et leur charge utile/plan de vol existant sera conservé.

Cette étape est similaire à toute autre mission, placez le groupe de votre choix, configurez-le, **enclenchez l'activation différée** et donnez-lui un nom facilement reconnaissable car il sera utilisé par la suite.


De plus, vous devrez créer une zone de déclenchement. Avec un nom reconnaissable (qui peut être identique à celui du groupe d'aéronefs) que nous utiliserons aussi par la suite.

Ceci est simplement pour avoir un point de référence pour la zone de déploiement du QRA, son rayon dans DCS n'a pas d'importance et sera défini ultérieurement.

## Mise en place dans le fichier missionConfig

Maintenant que le groupe existe dans la mission et a été configuré avec succès dans DCS, nous allons indiquer aux scripts qu'il s'agit d'un groupe QRA.

Cette étape doit être effectuée (ou refaite) avant d'exécuter le script de "build".

#### Options Logistique

De plus, il est possibble de contrôler étroitement la logistique de la QRA. Vous pouvez :


- Spécifier combien de déclenchements la QRA peut effectuer au départ (1 déclenchement = tous les groupes d'avion qui apparaîssent au lancement d'une QRA, les avions ne sont pas comptés individuellement ici)
  (vous pouvez utiliser cela pour rendre la QRA progressivement plus difficile à épuiser en commençant avec peu de déclenchements)

    :setQRAcount(\*QRAcount\*) --Supérieur ou égal à -1 : Nombre actuel d'ensembles d'avions disponibles pour le déploiement. Par défaut, cela est réglé sur -1, ce qui signifie qu'un nombre infini de groupes est disponible, aucune logistique ne prend place n'est effectué.
    
-> C'est votre "master arm" pour le reste de ces options.


- Spécifier le nombre de "places de parking" disponibles pour une QRA, qui ne peuvent pas être dépassées et, vu qu'elles sont limitées, peuvent conduire à l'épuisement de la QRA avec le temps

    :setQRAmaxCount(\*maxQRAcount\*) --Supérieur ou égal à -1 : Nombre maximal d'ensembles d'avions déployables à tout moment pour la QRA. Par défaut, cela est réglé sur -1, ce qui signifie qu'un nombre infini d'avions peut être accumulé pour le déploiement.
    
-> Exemple : Une QRA a 2 places sur 6 de remplies et prêtes pour le déploiement, 6 correspond au maxQRAcount, 2 au QRAcount actuel.


- Spécifier le nombre d'avions en "stockage" qui peuvent être expédiés aux "places de parking" mentionnées précédemment

    :setQRAmaxResupplyCount(\*maxResupplyCount\*) --Supérieur ou égal à -1 : Nombre total de groupes d'avions pouvant être provisionnés à la QRA. Par défaut, cela est réglé sur -1, ce qui signifie qu'une quantité infinie de stock est disponible. 0 signifie qu'aucun stock n'est disponible, aucun réapprovisionnement ne se produira, c'est votre "master arm" pour les réapprovisionnements. 
    
-> Prenons l'exemple précédent : nous avons besoin de 4 groupes (4 "places de parking" vides) mais nous n'en avons que 3 en stock pour réapprovisionner la QRA, 3 correspond au QRAmaxResupplyCount.
    

- Spécifier un seuil pour le nombre d'ensembles d'avions disponibles, en dessous duquel un réapprovisionnement est lancé :

    :setQRAminCountforResupply(\*minCountforResupply\*) --Égal à -1 ou supérieur à 0 : Nombre d'ensembles d'avions que la QRA doit avoir à tous temps, sinon un réapprovisionnement sera lancé. Par défaut, cela est réglé sur -1, ce qui signifie qu'un réapprovisionnement sera lancé dès qu'un ensemble d'avions est perdu.
    
-> Prenons l'exemple précédent : le nombre minimum d'ensembles d'avions déployables que nous souhaitons avoir en tout temps pour notre QRA est de 1, mais nous en avons 2, donc aucun réapprovisionnement ne se produira pour le moment. 1 correspond au minCountforResupply.
    

- Spécifier combien d'avions sont expédiés avec chaque réapprovionnements :

    :setResupplyAmount(\*resupplyAmount\*) --Supérieur ou égal à 1 : Nombre de groupes d'avions qui seront fournis à la QRA lorsqu'un réapprovisionnement se produit. Par défaut, cela est égal à 1. 
    
-> Prenons l'exemple précédent : nous venons de perdre nos deux znsembles, ce qui signifie que nous n'en avons plus, cela déclenchera un réapprovisionnement, qui apportera soit le nombre désiré d'ensembles soit le nombre d'avions que nous avons en stock si cette quantité est inférieure. Le réapprovisionnement sera également limité par le nombre maximum d'ensembles que nous pouvons avoir en même temps.
    

- Spécifier combien de temps prend un réapprovisionnement :

    :setQRAresupplyDelay(\*resupplyDelay\*) --Supérieur ou égal à 0 : Temps nécessaire pour effectuer un réapprovisionnement


**Plusieurs choses à noter ici :**

- Seul un réapprovisionnement peut avoir lieu à la fois, ils peuvent être programmés à chaque occasion possible (à chaque fois qu'un groupe QRA est perdu par exemple) mais n'auront lieu qu'un à la fois.
        
- Les groupes QRA qui viennent d'être provisionnés devront être réarmés (voir le délai et les contraintes associés [ici](#general-options))

#### Options pour le lien avec un aéroport / une base

Enfin, il est possible de lier les mécanismes de déploiement et de logistique de la QRA à la possesion (ou non) d'une base aérienne, d'un FARP, d'un navire ou d'un bâtiment par la coalition de la dite QRA :

    :setAirportLink("*airbase_name*") --Nom de l'unité / de la base aérienne : la QRA sera liée à cette base et cessera de fonctionner si la base est perdue (il peut s'agir d'un FARP (utilisez le nom de l'unité du FARP), d'un navire (utilisez le nom de l'unité du navire), d'un aérodrome ou d'un bâtiment (plateformes pétrolières, etc.))
    
**Non fonctionnel à ce jour :**

    :setAirportMinLifePercent(*value*) --Comprise entre 0 et 1 : pourcentage minimum de vie de l'aéroport lié pour que le QRA fonctionne. Les aéroports (pistes) et les navires ne devraient perdre de la vie que lorsqu'ils sont bombardés, cela nécessite des tests manuels pour savoir ce qui fonctionne le mieux.

**Plusieurs choses à noter ici :**

- La partie logistique est interrompue lorsque l'aérodrome est perdu.

- Une QRA qui est activée après la prise de son aérodrome par sa coalition devra être réarmée (voir le délai et les contraintes associés [ici](#general-options))

#### Dernière étape

Après avoir enchaîné ces options, vous pouvez ajouter cela :

    :start()

Comme son nom l'indique, cela démarre la QRA. Sinon, elle restera inactive.


Ou si vous souhaitez la démarrer ultérieurement dans l'un de vos scripts :

    if QRA_*NameOfQRA* then QRA_*NameOfQRA*:start() end


Vous pouvez également utiliser pour faire l'action inverse :

    if QRA_*NameOfQRA* then QRA_*NameOfQRA*:stop() end --utilisez ceci si vous souhaitez arrêter la QRA à un moment donné (dans un déclencheur, etc.). Elle peut être redémarrée en utilisant la méthode précédemment mentionnée.

Cela rend inactif la QRA (stop toute vérification d'aéroport, toutes les opérations de logistique, etc.).

### Options Avancées

En plus de ce qui a été dit précedemment, il est possible de modifier certaines options de "bas niveau".

#### Messages Joueur

Cette section concerne la modification des messages affichés aux joueurs des coalitions qui deuvent déclencher la QRA selon ses états :

    :setMessageStart("*value*") --Lorsque :start() est utilisé

    :setMessageDeploy("*value*") --Lorsque la QRA est déployée

    :setMessageDestroyed("*value*") --Lorsque la QRA est détruite

    :setMessageReady("*value*") --Lorsque la QRA est de nouveau prête au déploiement

    :setMessageOut("*value*") --Lorque la QRA est à court d'ensembles d'avions

    :setMessageResupplied("*value*") --Lorsque la QRA possède de nouveau des avions

    :setMessageAirbaseDown("*value*") --Lorsque la QRA perd sa base

    :setMessageAirbaseUp("*value*") --Lorsque la QRA regagne sa base

    :setMessageStop("*value*") --Lorsque :stop() est utilisé

## Contacts

Si vous avez besoin d'aide, ou si vous voulez suggérer quelque chose, vous pouvez :

* contacter [Rex][Rex on Github] sur GitHub
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
[VEAF-Open-Training-Mission-documentation]: https://www.veaf.org/opentraining

[demo-mission-structure]: ../images/demo-mission-structure.png