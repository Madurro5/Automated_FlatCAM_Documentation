# Code annoté

Cette page a pour but d'apporter plus de précisions sur le fonctionnement du plugin `Automated` et donc du code `ToolAutomated.py`.

> Les annotations sont organisées module par module.  
> Pour comprendre l'architecture globale : [Le Fork FlatCAM](fork-explication.md)

La base du code est une copie de `ToolDblSided.py`, le plugin servant au PCB double face.

## Organisation globale : un plugin dans l'architecture FlatCAM

Le projet repose sur une architecture modulaire où chaque fonctionnalité est encapsulée dans un **plugin**, un fichier Python autonome placé dans le dossier `appPlugin/`.

Le plugin `ToolAutomated` s'inscrit dans cette architecture. Il a été développé à partir d'une copie de `ToolDblSided.py` (le plugin de gestion double face), retenu comme base de travail pour deux raisons : il exposait déjà les mécanismes de miroir et de trous d'alignement, et sa structure deux-classes (logique + UI) était représentative du patron de conception utilisé dans l'ensemble du projet.
 
---
 
## Comment les fichiers s'articulent entre eux
 
Le plugin ne vit pas seul. Il s'appuie sur plusieurs couches de FlatCAM avec lesquelles il interagit sans jamais les modifier :
 
**`AppTool` (appTool.py)** — classe de base dont hérite `ToolAutomated`. Elle fournit les mécanismes d'enregistrement du plugin, la gestion de l'onglet dans le panneau latéral, et l'interface commune avec le reste de l'application (`self.app`).
 
**`appGUI/GUIElements.py`** — bibliothèque interne de widgets Qt personnalisés. Le plugin y puise l'ensemble de ses composants visuels : `FCLabel`, `FCButton`, `FCEntry`, `FCComboBox`, `FCDoubleSpinner`, `RadioSet`, `GLay`, `FCFrame`, etc. Ces éléments encapsulent PyQt6 avec les conventions visuelles de FlatCAM (couleurs, précision décimale, signaux personnalisés).
 
**`appObjects/ScriptObject.py`** — objet FlatCAM représentant un script TCL. Le plugin l'utilise pour créer dynamiquement un script en mémoire et le soumettre à l'interpréteur FlatCAM via `self.app.run_script`.
 
**`self.app.collection`** — modèle de données central de FlatCAM qui liste tous les objets chargés (Gerber, Excellon, Geometry). Les combos de sélection du plugin y sont directement branchés via `setModel()` et `setRootModelIndex()`.
 
**L'interpréteur TCL de FlatCAM** — couche d'exécution sur laquelle repose tout le workflow d'automatisation. Les commandes `isolate`, `cncjob`, `drillcncjob`, `write_gcode`, `mirror`, `set_origin`, `plot_all` sont des commandes TCL natives exposées par FlatCAM. Le plugin les assemble dans un script texte, puis délègue l'exécution complète.
 
```
ToolAutomated.py
    │
    ├── hérite de ──────────────► AppTool (appTool.py)
    │                                 └── accède à self.app (FlatCAM core)
    │
    ├── compose avec ──────────► AutoUI
    │                                 └── utilise appGUI/GUIElements.py
    │
    ├── interagit avec ────────► self.app.collection  (objets chargés)
    ├── émet vers ─────────────► self.app.run_script  (interpréteur TCL)
    └── crée dans ─────────────► ScriptObject         (vecteur d'exécution)
```
 
---
 
## Point de départ du développement
 
La première décision a été de **dupliquer `ToolDblSided.py`** plutôt que de partir de zéro. Ce fichier contenait déjà :

- la mécanique de miroir (indispensable pour le PCB double face),

- la création de trous d'alignement en Excellon,

- la connexion aux événements souris pour sélectionner un point de référence sur le canvas.

Tout ce socle a été conservé, retouché, et enrichi et constitue aujourd'hui la partie "Advanced" du plugin. Le travail de développement a ensuite consisté à **construire par-dessus** : ajout de la génération du script TCL, du système de configurations, du workflow PCB et SVG et de la sélection du répertoire de sortie des fichiers convertis.
 
---
 
## Les deux classes principales
 
Le fichier `ToolAutomated.py` est structuré autour de trois classes, dont deux principales, qui se répartissent les responsabilités de manière claire et indépendante.
 
### `ToolAutomated` — la logique métier
 
C'est la classe contrôleur. Elle hérite de `AppTool`, s'enregistre auprès de FlatCAM, et orchestre l'ensemble des opérations. À l'initialisation, elle instancie `AutoUI`, connecte tous les signaux aux gestionnaires d'événements, et initialise l'état interne du plugin.
 
Son rôle principal est de **répondre aux actions utilisateur et de déclencher les traitements** : générer le script TCL, l'exécuter, gérer les configurations, piloter la visibilité des sections de l'UI selon le mode actif et bien plus encore ... .
 
### `AutoUI` — l'interface graphique
 
C'est la classe vue. Elle construit l'intégralité du panneau latéral en PyQt6, section par section, en instanciant et disposant les widgets dans des layouts. Elle ne contient aucune logique métier — uniquement de la construction d'interface et quelques méthodes utilitaires de lecture d'état (`get_workflow()`).
 
Cette séparation nette entre logique et vue permet de modifier l'interface sans toucher aux algorithmes, et inversement.
 
---
 
## Détail des mécanismes clés
 
### Le système de configurations d'usinage
 
Avant même les deux classes, le fichier définit des structures de données qui constituent la colonne vertébrale du plugin.
 
**`_make_default_config()`** est une fonction usine qui retourne un dictionnaire de configuration vierge avec des valeurs par défaut raisonnables. Cette fonction existe pour éviter les références partagées entre configurations — chaque appel produit une copie indépendante.
 
**`BUILTIN_CONFIGS`** est un dictionnaire global contenant les configurations intégrées (`Default PCB`, `Default Geometry`). Ces configurations sont immuables : le plugin refuse explicitement de les écraser ou de les supprimer.
 
**`@dataclass Parameters`** est un conteneur typé qui matérialise une configuration active en trois groupes de paramètres :

-    `isolate_param` — paramètres pour la passe d'isolation (diamètre fraise, nombre de passes, recouvrement, type d'isolation),

-    `cncjob_param` — paramètres pour la génération du job CNC (profondeur de coupe, vitesses, broche, post-processeur),

-    `drillcncjob_param` — paramètres pour le perçage Excellon (diamètre percé, profondeur, changement d'outil).

La méthode de classe `Parameters.from_config()` construit une instance à partir d'un dictionnaire de configuration, en effectuant systématiquement une copie profonde pour éviter les effets de bord entre configurations.
 
### Le mode Beginner / Advanced
 
Le bouton `level` en haut du panneau est un `QToolButton` à deux états (checkable). Selon qu'il est coché ou non, `on_level_changed()` affiche ou masque des sections entières de l'UI :
 
- **Mode Beginner (vert)** : seuls les essentiels sont visibles — sélection des fichiers, répertoire de sortie, choix de configuration, bouton Run. L'utilisateur ne peut pas se perdre dans les paramètres.
- **Mode Advanced (rouge)** : les sections Bounding Box, Mirror, PCB Alignment et le panneau de paramètres d'usinage détaillés deviennent accessibles.

Cette approche est délibérée : elle permet d'utiliser le plugin de manière totalement automatisée en mode Beginner, et de contrôler finement chaque paramètre en mode Advanced. La structure de base de cette fonctionnalité est entièrement copié sur le plugin `ToolDblSided.py`.
 
### Les deux workflows : PCB et SVG
 
Le combo `Workflow` en haut du panneau pilote un basculement entre deux frames Qt mutuellement exclusives :
 
**Workflow PCB** — prévu pour les fichiers issus de KiCad ou équivalent. L'utilisateur sélectionne jusqu'à quatre objets : `Edge_Cut` (le contour du PCB), `F_Cu` (cuivre face avant), `B_Cu` (cuivre face arrière, optionnel pour le double face), et un fichier `Excellon` pour les perçages.
 
**Workflow SVG** — prévu pour les géométries importées depuis un fichier SVG, typiquement pour de la gravure ou découpe. Dans FlatCAM, un SVG importé devient un objet `Geometry` qui ne nécessite pas de passe d'isolation : le `cncjob` est appliqué directement. Un fichier Excellon reste optionnel.
 
### La génération TCL — `generate_tcl_script()`
 
C'est la méthode centrale du plugin. Elle construit une chaîne de caractères contenant un script TCL complet, prêt à être soumis à l'interpréteur FlatCAM. La séquence générée pour le workflow PCB suit scrupuleusement les étapes manuelles que ferait un opérateur :
 
1. `set_origin -auto 1` — recentrage automatique de l'origine,
2. `set_active` sur chaque objet — activation dans FlatCAM,
3. `mirror B_Cu -axis X -box Edge_Cut` — miroir du cuivre arrière (double face uniquement),
4. `isolate` sur chaque couche Gerber — création des chemins d'isolation,
5. `drillcncjob` sur l'Excellon — génération du job de perçage,
6. `cncjob` sur chaque géométrie isolée — génération des jobs CNC de gravure,
7. `write_gcode` pour chaque job — export des fichiers G-Code dans le sous-dossier `output/`.
Tous les paramètres numériques (profondeurs, vitesses, diamètres, post-processeur) sont injectés dynamiquement depuis l'instance `Parameters` résolue à partir de la configuration active.
 
### L'exécution via `execute_custom_script()`
 
Une fois le script TCL généré, il n'est pas écrit sur disque mais injecté directement dans un objet `ScriptObject` créé en mémoire dans la collection FlatCAM, puis soumis via le signal `self.app.run_script`. FlatCAM prend alors le relais et exécute chaque commande TCL séquentiellement, exactement comme si l'utilisateur les avait tapées une par une dans la console.
 
### La gestion des configurations utilisateur
 
En mode Advanced, trois boutons permettent de gérer des configurations personnalisées stockées dans `self.user_configs` (dictionnaire d'instance, non persistant entre sessions) :
 
- **Ajouter** (`on_config_add`) — ouvre une boîte de dialogue de saisie du nom, crée une copie de la configuration actuellement sélectionnée,
- **Modifier** (`on_config_edit`) — lit les valeurs actuelles de tous les spinners Advanced et les sauvegarde. Si la configuration active est une config intégrée, elle est automatiquement clonée sous un nouveau nom suffixé `_custom`,
- **Supprimer** (`on_config_delete`) — supprime une configuration utilisateur après confirmation. Les configs intégrées sont protégées.
### Les outils d'alignement (hérités de `ToolDblSided` et enrichis)
 
La section PCB Alignment, visible uniquement en mode Advanced, permet de créer un fichier Excellon de trous d'alignement (`Alignment_Drills`) puis de le convertir directement en G-Code via `alignment_drill_tcl_script()`. Trois modes de placement sont supportés : miroir selon X, miroir selon Y, ou manuel (coordonnées libres). Le point de référence pour le miroir peut être saisi manuellement, calculé depuis la bounding box d'un objet existant, ou capturé en cliquant directement sur un trou dans le canvas — ce dernier cas mobilisant la gestion des événements souris hérités de `ToolDblSided` (`on_pick_hole`, `on_mouse_click_release`).