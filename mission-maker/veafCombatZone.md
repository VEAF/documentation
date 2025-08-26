# Combat Zones — Guide pour mission makers

## Table des matières
- Principe
- Fonctions de base
  - Définir une combat zone (éditeur)
  - Méthodes obligatoires / comportement minimal
  - Activation / désactivation et menu radio
- Fonctions optionnelles (triées par intérêt)
  - Commandes via `#command` (très puissant)
  - Contrôle du spawn : `#spawngroup`, `#spawnradius`, `#spawncount`, `#spawnchance`, `#spawndelay`
  - Chaînage de zones
  - Nettoyage & suivi (spawnedGroups)
  - Demandes joueurs : smoke / flare / info
- Exemples pratiques
- Notes et bonnes pratiques

---

## Principe
Une "combat zone" gérée par le module `veafCombatZone` est une Trigger Zone DCS définie par le concepteur de mission et interprétée au démarrage par le script.

- Le script lit toutes les unités placées dans la Trigger Zone au démarrage pour construire la description de la zone (éléments de zone). Ces unités servent de définitions : groupes à respawner, statics, ou "fake units" portant des commandes VEAF.
- Après lecture la zone est mise dans un état propre : toutes les unités présentes dans la zone sont détruites (pour éviter les doublons au moment du spawn).
- L'activation d'une zone exécute la logique définie par les éléments : réapparition de groupes, exécution de commandes VEAF, envoi de groupes sur des routes, etc.
- Le module ajoute automatiquement un sous-menu radio `COMBAT ZONES` (via `veafRadio`) permettant aux joueurs d'activer / désactiver des zones, demander des infos, des marquages (smoke/flare), etc.

Le système est conçu pour permettre aux mission makers d'exprimer le comportement d'une zone sans écrire de Lua : tout se fait via Trigger Zone + unités + balises placées dans le nom des unités.

---

## Fonctions de base

### Définir une combat zone (éditeur)
Étapes minimales :
1. Créer une Trigger Zone dans l'éditeur DCS. Donnez-lui un nom technique, par ex. `CZ_Kobuleti`.
2. Placer dans la Trigger Zone des unités qui décriront la logique :
   - un groupe complet (ou une de ses unités) pour indiquer un template à respawner ;
   - une unité "marqueur" (static ou unité) dont le nom contiendra une balise `#command="..."` pour exécuter une commande VEAF à l'activation.
3. Charger `veafCombatZone` dans vos scripts (inclus dans l'archive VEAF). Le module lira la zone au démarrage.

Remarques :
- Si vous ne donnez pas de nom "friendly" via le script, le module utilise le nom technique de la zone.
- Le script supprime les unités physiques trouvées dans la zone au démarrage (comportement voulu pour laisser place aux respawns).

### Méthodes obligatoires / comportement minimal
Pour un usage sans script additionnel, vous devez :
- Créer la Trigger Zone et y placer au moins un élément (groupe ou marqueur).
- Charger `veafCombatZone`.

API utile (appelable par script ou via raccourcis):
- `veafCombatZone.GetZone(zoneName)` → retourne l'objet zone (type `VeafCombatZone`).
- `veafCombatZone.ActivateZone(zoneName, silent?)` → active la zone.
- `veafCombatZone.DesactivateZone(zoneName, silent?)` → désactive la zone.
- `veafCombatZone.SmokeZone(zoneName)` → pop smoke sur la zone.
- `veafCombatZone.LightUpZone(zoneName)` → illumination flare.
- `veafCombatZone.GetInformationOnZone({zoneName, unitName?})` → affiche l'information/briefing de la zone.

Ces fonctions sont utilisées par le menu radio et peuvent être invoquées dans d'autres scripts si besoin.

### Activation / désactivation et menu radio
- Par défaut, le module génère un menu radio `COMBAT ZONES` listant les zones (si `enableRadioMenu` activé).
- Les joueurs peuvent activer/désactiver une zone, demander un briefing, demander un marquage (smoke/flare).
- Pour forcer l'activation depuis un script :

```lua
local z = veafCombatZone.GetZone("CZ_Kobuleti")
if z then
  veafCombatZone.ActivateZone(z:getMissionEditorZoneName())
end
```

---

## Fonctions optionnelles (triées par intérêt)

### A) Commandes via `#command` (très puissant)
- Placez une unité "marqueur" dans la Trigger Zone dont le nom contient : `#command="<commande veaf>"`.
- Exemples de commandes valides :
  - `#command="_spawn samgroup, size 1, defense 4"`
  - `#command="-samlr"` (alias)
  - `#command="_cas, defense 3, size 5"`
- Le module ajoute automatiquement `, czName <zoneName>` à la commande avant de l'exécuter (la commande peut utiliser `czName`).
- Les commandes sont passées à `veafInterpreter.execute()` qui s'occupe d'appeler le module cible (veafSpawn, veafCasMission, veafShortcuts, etc.).

Usage recommandé : gérer des templates complexes via `_spawn` plutôt que tenter d'exprimer tout via des statics/groupe simples.

### B) Contrôle du spawn : balises disponibles
Vous pouvez enrichir le nom des unités/marqueurs avec les balises suivantes (exemple : `Marker1 #spawngroup="armorgroup" #spawnradius=500 #spawnchance=75`):

- `#spawngroup="name"` : si la valeur est différente du nom de l'unité, indique le template (spawn group) à utiliser.
- `#spawnradius=NNN` : rayon en mètres pour positionner aléatoirement le respawn autour du point (ex : `#spawnradius=500`).
- `#spawncount=N` : nombre minimum d'éléments à faire apparaître pour un groupe logique.
- `#spawnchance=XX` : probabilité (%) qu'un élément soit spawné.
- `#spawndelay=NNN` : délai, en secondes, avant d'exécuter le spawn (utile pour étaler l'apparition).

Comportement : lors de l'activation, pour chaque `spawnGroup` le script calcule quels éléments spawnent selon `spawnchance` et `spawncount`, positionne chaque spawn aléatoirement selon `spawnradius`, puis respawn avec `mist.teleportToPoint` ou exécute la commande associée.

### C) Chaînage de zones
- Permet d'activer une zone suivante automatiquement quand la zone courante est complétée.
- API script :
  - `zone:addChainedCombatZone("OtherZoneName")`
  - `zone:setChainedCombatZonesDelay("1-5")` — la valeur peut être un intervalle aléatoire.
- Utilisation : créer des campagnes/opérations séquentielles sans intervention manuelle.

### D) Nettoyage & suivi (spawnedGroups)
- Le module garde une liste `zone.spawnedGroups` des groupes créés par la zone.
- Fonctions utiles :
  - `zone:addSpawnedGroup(groupName)`
  - `zone:getSpawnedGroups()`
  - `zone:clearSpawnedGroups()`
  - `zone:disableJunkCleanup()` — si vous voulez conserver les unités après complétion.
- Par défaut, quand la zone est complétée, le module peut nettoyer les unités spawnées. Si vous voulez garder des éléments pour la suite, désactivez le cleanup.

### E) Demandes joueurs (smoke / flare / info)
- Menu radio expose : briefing, coordonnées, `Pop smoke` (marquage), `Request illumination flare`.
- API :
  - `veafCombatZone.SmokeZone(zoneName)`
  - `veafCombatZone.LightUpZone(zoneName)`
  - `veafCombatZone.GetInformationOnZone({zoneName, unitName?})`
- Paramètres pertinents côté zone : `enableSmokeAndFlare`, `enableRadioMenu`.

---

## Exemples pratiques

### 1) Respawn simple d'un groupe existant
- Trigger Zone : `CZ_A`
- Placez un groupe `opfor_patrol` dans la zone.
- (Optionnel) renommez une unité : `opfor_patrol #spawnradius=250 #spawncount=2`
- À l'activation (`veafCombatZone.ActivateZone("CZ_A")`), le module respawn `opfor_patrol` selon les paramètres.

### 2) Spawn via commande VEAF
- Marqueur nommé : `cmd_spawn_1 #command="_spawn samgroup, size 1, defense 4"`
- À l'activation, `_spawn...` sera exécuté (avec `czName` ajouté automatiquement).

### 3) Exemple combiné
- `marker1 #spawngroup="armorgroup" #spawnradius=500 #spawnchance=75 #spawndelay=10`
- Résultat : à l'activation, le module décidera aléatoirement (75%) de faire apparaître l'élément, choisira une position dans le cercle 500m, puis l'exécutera après 10s.

---

## Notes & bonnes pratiques
- Préfixez techniquement vos zones (ex : `CZ_`) pour lisibilité et cohérence.
- Testez toujours en mission réelle : le comportement (respawn, suppression initiale) est uniquement observable en runtime.
- Pour templates complexes privilégiez `#command` + `_spawn`/`veafSpawn` afin de centraliser la logique de templates.
- Si vous utilisez chaînage / hooks avancés, préférez appeler les fonctions publiques via un script d'initialisation :

```lua
local z = veafCombatZone.GetZone("CZ_A")
if z then
  z:addChainedCombatZone("CZ_B")
  z:setChainedCombatZonesDelay("5-15")
end
```

- Pour débogage : augmentez le niveau de log de `veafCombatZone` (changer `veafCombatZone.LogLevel` dans le code) pour voir le parsing des balises, les décisions de spawn et le nettoyage.

---

---

## Exemples tirés de la configuration (OpenTraining — Caucase)

La configuration de la mission OpenTraining (Caucase) fournie dans `missionConfig.lua` illustre des usages concrets et une activation automatique d'une zone. Extraits utiles :

- Activation automatique d'une zone dans `missionConfig.lua` :

```lua
-- automatiquement activer la zone "Maykop Defenses" au démarrage
veafCombatZone.ActivateZone("combatZone_MaykopDefenses", true)
```

Cet exemple montre comment la configuration peut activer une zone précise sans intervention manuelle. Dans ce cas, la zone technique attendue dans l'éditeur doit s'appeler `combatZone_MaykopDefenses`.

### Autres réglages observés dans OpenTraining
- Le fichier initialise de nombreux modules avant d'appeler l'activation des zones (notamment `veafRadio`, `veafSpawn`, `veafInterpreter`, etc.). Assurez-vous que `veafCombatZone` est initialisé après ces modules si vous dépendez d'eux.

### Comment réutiliser ces extraits
- Pour activer automatiquement une zone au démarrage dans votre propre mission, ajoutez la ligne ci-dessus dans `missionConfig.lua` après l'initialisation des modules et avant la fin du fichier.
- Pour chaîner ou modifier des zones à la volée, obtention de l'objet zone et utilisation des méthodes exposées :

```lua
local z = veafCombatZone.GetZone("combatZone_MaykopDefenses")
if z then
  z:addChainedCombatZone("anotherZoneName")
  z:setChainedCombatZonesDelay("5-15")
  z:disableRadioMenu() -- exemple de modification
end
```

Ces extraits sont directement applicables dans la mission OpenTraining et servent de modèle pour vos propres missions.

---

## Checklist rapide (pour mission makers)
Copiez-collez cette checklist dans votre workflow pour créer et tester rapidement une `Combat Zone` :

1. Dans l'éditeur DCS, créez une Trigger Zone et nommez-la clairement, ex : `CZ_MyZone`.
2. Placez au moins un élément dans la zone :
  - un groupe (ou une unité du groupe) pour indiquer un template à respawner, ou
  - une unité marqueur avec `#command="..."` pour exécuter une commande VEAF.
3. (Optionnel) Ajoutez des balises utiles dans le nom de l'unité :
  - `#spawngroup="name"`, `#spawnradius=NNN`, `#spawncount=N`, `#spawnchance=XX`, `#spawndelay=NNN`.
4. Dans `missionConfig.lua`, assurez-vous que `veafCombatZone` est initialisé puis :
  - pour activation automatique : `veafCombatZone.GetZone("CZ_MyZone"):activate()`;
  - pour tests rapides, réduire `veafCombatZone.SecondsBetweenWatchdogChecks`.
5. Démarrez la mission en local et vérifiez :
  - que la zone a été ``nettoyée`` (unités initiales détruites),
  - que les spawns / commandes s'exécutent lors de l'activation,
  - le menu radio `COMBAT ZONES` propose vos actions (si activé).
6. Debug : augmenter `veafCombatZone.LogLevel` pour obtenir plus d'informations dans `DCS.log`.

---

## Exemples concrets (copier-coller)
Utilisez ces extraits directement dans l'éditeur (`nom d'unité`) ou dans `missionConfig.lua`.

1) Marqueur qui exécute une commande VEAF (ex : spawn template samgroup) :

```
Marker_SAM #command="_spawn samgroup, size 1, defense 4"
```

2) Marqueur qui contrôle le spawn (rayon, chance, délai) :

```
Marker_Armor #spawngroup="armorgroup" #spawnradius=500 #spawnchance=75 #spawndelay=10
```

3) Activation automatique depuis `missionConfig.lua` (extrait de la mission démo) :

```lua
-- activer une zone au démarrage
veafCombatZone.GetZone("czCrossKobuleti"):activate()
veafCombatZone.GetZone("czBatumi"):activate()
```

4) Exemple de script d'ajout de chaîne entre zones :

```lua
local z = veafCombatZone.GetZone("CZ_A")
if z then
  z:addChainedCombatZone("CZ_B")
  z:setChainedCombatZonesDelay("5-15")
end
```

---

## Indication pour captures d'écran (si vous voulez ajouter des images)
Les captures suivantes ont été ajoutées dans `documentation/images/` et sont insérées ci-dessous :

![Trigger Zone dans l'éditeur](images/CZ_editor_zone.png)
*Capture : Trigger Zone dans l'éditeur (nom technique et centre).* 

![Étiquettes de nom d'unité avec balises](images/CZ_unit_name_tags.png)
*Capture : Inspecteur d'une unité montrant `#command` et `#spawn*` dans le nom.* 

![Menu radio COMBAT ZONES](images/CZ_radio_menu.png)
*Capture : Menu radio en jeu affichant l'entrée `COMBAT ZONES` et les actions disponibles.* 

Si vous préférez d'autres emplacements ou légendes, dites-moi lesquelles et je les mettrai à jour.

---

Si vous voulez, je peux générer également la checklist au format court séparé (`documentation/CZ-checklist.md`) ou insérer les captures si vous les uploadez.

