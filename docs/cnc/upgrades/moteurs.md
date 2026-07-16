# Moteurs

## Contexte et motivation

Les drivers moteurs TB6600 remplaçant les anciennes cartes de gestion des moteurs de chacun des axes permettent de délivrer un courant plus important en sortie ce qui physiquement offre plus de couple. Pour bénéficier du courant maximale délivrable par les chacun des 3 TB6600 il faut remplacer les moteurs par des NEMA23 3A. 

## Matériel utilisé

Des moteurs de ce type ont été commandé, la seule différence notable se situe au niveau de l'axe qui présente un méplat (ou axe en D). Les anciens moteurs n'en sont par pourvus.

![Photo NEMA23]()

Différentes mesures et prototypes ont été réalisée pour reproduire un engrenage adapté à l'axe en D de chacun des 3 moteurs. Des engrenages normalisés ont donc été commandés.

![Photo Engrenage Impression 3D]()
![Photo Engrenage commandé]()

## Procédure d'installation et de calibration

Enlever les moteurs actuellement présents avec leur roue dentée montée sur l'axe.
Placer les nouveaux engrenages avec méplats sur l'axe des noueaux moteurs pas à pas. Remonter à l'identique ces moteurs aux emplacement des précédents pour chacun des axes. 

Une fois monté il faut calibrer le logiciel OpenBuild Control aux nouveaux moteurs. Pour se faire, préparer la cnc comme pour un usinage ([voir guide d'utilisation cnc](../guide-utilisation.md)).

Lancer le logiciel OpenBuild Control sur l'ordinateur et connecter vous à l'arduino (GRBL) du boîtierr de commande depuis l'interface du logiciel.

OpenBuild Control propose un outil interne pour calibrer les moteurs pas à pas. Il suffit simplement d'être précis, méticuleux et de suivre les étapes correctement.

![Photo 1 outil calibration Moteur]()

![Photo 2 outil calibration Moteur]()

![Photo 3 outil calibration Moteur]()

Cet outil permet d'obtenir les paramètres optimaux en terme de précision pour le contrôle des moteurs. Ces paramètres font parties des paramètres GRBL au sein de la carte Arduino.

## Résultat et observations

Les moteurs n'ayant pas encore été remplacés, vous seul verrez les résultats de ces changements.

→ Retour : [Upgrades](index.md)
