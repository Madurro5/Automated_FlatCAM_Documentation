# Chaîne logicielle

Vue d'ensemble des logiciels qui composent la chaîne de traitement, de la conception à l'usinage.

## Rôle de chaque logiciel

| Logiciel | Rôle dans la chaîne |
|---|---|
| [KiCad](kicad.md) | Conception du PCB et export Gerber |
| [FlatCAM Fork](flatcam-fork/index.md) | Conversion Gerber → G-code, gestion des passes d'usinage |
| [OpenBuilds Control](openbuilds-control.md) | Interface de contrôle machine, envoi du G-code, GRBL |

## Flux de données

La chaîne logicielle représentes l'ensemble des blocs de couleur rouge du schéma du fonctionnement global ci-dessous.

```mermaid
flowchart TD
    classDef redBlock fill:#ff4d4d,stroke:#333,stroke-width:2px,color:#fff;
    class A,B,C redBlock;

    A[KiCad]:::redBlock -->|Export .gbr / .drl| B[FlatCAM Fork]:::redBlock
    B -->|Export .nc / .gcode| C[OpenBuilds Control]:::redBlock
    C -->|USB / Serial| D[Contrôleur GRBL]
    D -->|Commandes électriques| E[Machine CNC]

    linkStyle 0,1,2 stroke:#ff4d4d,stroke-width:2px;
    linkStyle 3 stroke:#333,stroke-width:2px;
```

--------------------------------------------------------------

[→ Première étape : KiCAD](kicad.md)

