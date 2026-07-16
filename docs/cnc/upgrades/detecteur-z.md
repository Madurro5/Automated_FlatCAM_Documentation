# Detecteur de surface 

## Contexte et motivation

Pour chaque usinage avec la machine CNC, une mise à 0 de l'axe Z est nécessaire. Jusqu'à présent cette mise à zéro devait être réalisée visuellement avec un rapprochement de l'outil monté sur la broche au plus proche de la surface à usiner. Ce processus est long, peu pratique et potentiellement problématique si l'opérateur n'arrête pas la descente de l'outil au bon moment et abîme la pointe de l'outil.

Une fonction de détection de surface permet de faire descendre automatiquement l'outil jusqu'au contact électrique entre l'outil et la surface à usiner.

## Détection de surface et GRBL

Il s'avère que GRBL sur Arduino UNO R3 permet déja de faire de la détection de surface.
Les schémas fournis par GRBL nous apprennent que la pin A5 de l'ARDUINO est dédié à la déteciton de surface. Il suffit de relier cette pin à la masse ou à un +3,3V pour décelencher une détection au sein du GRBL qui sera remonté par liaison série à OpenBuild Control.


## Modification des Cartes du boîtier
Cette pin n'étant physiquement pas relié sur la carte GRBL (Carte Série) du boîtier de commande de la CNC, il a fallut apporter quelques modifications électroniques sur cette carte et la carte d'entrée/sorties.

![Photo des cartes modifées]()

![Photo Carte GRBL dessus]()

![Photo Carte GRBL dessous]()

![Photo carte Entrée sorties dessus]()

![Photo carte Entrée sorties dessous]()

![Photo Connecteur Entrée sortie pin utilisées]()

![Photo connectique table percage]()

## Sonde de détection

Une sonde confectionné "à la main" sert à la détection de surface. Il s'agit de deux petits câbles blindés, un connecté à l'outil fixé à la broche et l'autre à la plaque cuivré à usiner dont on veut détecter la surface ou à un petit support de détection dont on connait avec précision la hauteur lorsque l'on usine des matériaux non conducteurs (pour détecter la surface d'une pièce de bois ou autre).

![Photo Sonde fabriquée main]()

## Palpeur de détection de surface

Il reste préférable d'utiliser un palpeur à placer sous l'outil. Le palpeur présente un léger ressort ce qui prévient contre une dégradation de la pointe de l'outil qui descend sans être en rotation.


![Photo palpeur détection de surface]()

## Résultat et observations

→ Retour : [Upgrades](index.md)
