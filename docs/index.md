# Documentation CNC

Bienvenue dans la documentation complète de la CNC.

## Ce que couvre cette documentation

| Section | Fonction |Contenu |
|---|---|---|
| [Chaîne logicielle](chaine-logicielle/index.md) | Préparation des fichiers d'usinage, commandes d'usinage | KiCad, FlatCAM, OpenBuilds Control |
| [Machine CNC](cnc/index.md) | Execution des commandes d'usinage | Description matérielle, guide d'utilisation, upgrades |

## La chaîne complète en un coup d'œil

```mermaid
flowchart LR
    A[KiCad\nConception PCB] --> B[FlatCAM Fork\nGénération G-code]
    B --> C[OpenBuilds Control\nEnvoi commandes G-code]
    C --> D[Boîtier Traitement de signal\nContrôle des moteurs]
    D --> E[Moteurs et Broche\nUsinage]
```

## Comment utiliser cette documentation

Cette documentation technique est entièrement rédigée en Markdown (.md) puis convertie en site statique grâce à [Mkdoc](https://www.mkdocs.org/).

Ouvrir `index.html` à la racine du dossier `site/` de documentation permet d'ouvrir la doc localement comme un site statique.


- La **barre de navigation à gauche** liste toutes les sections et pages.
- La **barre de navigation à droite** liste toutes les sections au sein de la pages.
- La **barre de recherche** en haut permet de trouver n'importe quel terme.
- Chaque page liée dans le diagramme CNC renvoie vers la fiche détaillée du composant matériel concerné.

## Comment alimenter ou apporter des modifications à cette documentation

1. Récupérer le dossier de documentation et suivre la configuration décrite dans le [`README.md`](../../README.md) 
2. Éditer le fichier `.md` correspondant dans `docs/`.
3. Pour ajouter une page : la créer dans `docs/` puis l'ajouter dans la section `nav:` dans `mkdocs.yml`.
4. Lancer la commande `mkdocs serve` pour prévisualiser (`mkdocs serve --livereload --watch docs/` pour auto-refresh à la modification d'un fichier). 
5. Une fois la documentation complète lancer la commande `mkdocs build` pour générer le site statique.

:bulb: tip "Distribution hors-ligne"
    Le dossier `site/` généré par `mkdocs build` est entièrement autonome.
    Il s'ouvre depuis un disque dur ou une clé USB sans connexion internet.
