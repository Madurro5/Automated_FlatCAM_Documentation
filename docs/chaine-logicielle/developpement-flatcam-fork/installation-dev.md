# Installation du code — Environnement de développement

> ## Prérequis

> Le code a été développé sur Windows 10 (version de 2018 ou plus). Veuillez avoir sur votre machine les éléments suivants :

> → VS Code installé sur votre machine (ou un autre IDE de développement)

> → Avoir installé Python 3.6 ou ultérieur. Le plugin a été développé avec Python 3.10, c'est donc la version à privilégier.  

## Cloner le dépôt

#### Avant de commencer
Dans un premier temps, créez un dossier global qui va accueillir le code.
Placez-vous dans ce dossier avec votre terminal ou PowerShell.

#### Commande git clone [...]
Le dépôt est accessible à l'adresse suivante :
[Automated FlatCAM](https://github.com/Madurro5/Automated_FlatCAM_Fork).

Dans ce cas, clonez le dépôt directement sur votre PC dans le dossier créé à l'aide de la commande suivante :

```bash
git clone [https://github.com/Madurro5/Automated_FlatCAM_Fork.git](https://github.com/Madurro5/Automated_FlatCAM_Fork.git)
```

#### Récupération classique de fichiers

Sinon, récupérez le dossier du projet qu'on vous a fourni (support physique ou accès cloud) et faites-en une copie dans votre dossier global.

#### Création de l'environnement virtuel

Il ne reste plus qu'à créer l'environnement virtuel Python dans lequel les dépendances seront installées.

Pour cela, placez-vous à la racine de votre `Dossier-global` avec le terminal.

Vérifiez d'abord que Python 3.10 ou ultérieur est bien installé sur votre machine avec cette commande :

```
py -0
```

Si ```Python 3.10.1``` vous est retourné dans la liste, alors Python 3.10 est bien installé sur votre machine.

Créez puis activez l'environnement virtuel avec les commandes suivantes :

```
py -3.10 -m venv env310
.\env310\Scripts\activate
python --version
```

L'environnement Python est à ce stade normalement installé et activé, avec la confirmation de la version affichée dans le terminal.

## Structure des dossiers du projet

L'architecture dans vos dossiers va et doit ressembler à ceci :

```
Dossier-global/
├── .env310/         # Environnement virtuel (exclu via .gitignore)
└── flatcam/         # Dépôt GitHub cloné
    ├── .gitignore   # Doit contenir ".env310/" pour ignorer l'environnement virtuel mais aussi build/, dist/ et autre
    ├── README.md
    ├── requirements_env310.txt
    └── ...          # Tous les fichiers du projet Automated flatCAM
```

## Installer les dépendances

Pour pouvoir fonctionner, FlatCAM repose sur de nombreuses dépendances (ou bibliothèques) Python qu'il est impératif d'installer au sein de l'environnement virtuel.

L'ensemble des dépendances et les consignes pour les installer sont consignées dans le fichier `requirements_env310.txt`. 
Les consignes d'installation sont les suivantes:

---

#### ORDRE D'INSTALLATION IMPORTANT:

Installation manuelle de `GDAL` :

```
# 1. GDAL (must be installed manually via wheel BEFORE the rest):
#    Download: geoai_GDAL-3.4.3-cp310-cp310-win_amd64.whl
#    URL: https://pypi.org/project/geoai-GDAL/#files
#    Install: pip install "path\to\geoai_GDAL-3.4.3-cp310-cp310-win_amd64.whl"
#    Test:    python -c "from osgeo import gdal; print(gdal.__version__)"
```

> Si toutefois le lien de téléchargement n'est pas valide, le projet (dossier contenant le projet) contient le wheels fonctionnel de GDAL à l'emplacement : `flatcam/assets/Working GDAL-RASTERIO wheels/GDAL-3.3.3-cp310-cp310-win_amd64.whl`.
> Pour installer le package GDAL du projet, entrez la commande : 

```
pip install "flatcam\assets\Working GDAL-RASTERIO wheels\geoai_GDAL-3.4.3-cp310-cp310-win_amd64.whl"
```

Enfin, le reste des dépendances à installer, notées dans le fichier `requirements_env310.txt`, s'installe de la façon suivante : 
Placez-vous au sein du dossier du projet (`flatcam/`) et assurez-vous que l'`env310` est actif.

```
pip install -r requirements_env310.txt
```

NOTE: pycairo peut avoir besoin de Visual C++ Build Tools pour Windows.
      Téléchargez-le via le lien suivant: https://visualstudio.microsoft.com/visual-cpp-build-tools/



> Si l'installation de `Rasterio` ne fonctionne pas ou pose problème, le projet (dossier contenant le projet) contient le wheels fonctionnel de Rasterio à l'emplacement : `flatcam/assets/Working GDAL-RASTERIO wheels/rasterio-1.2.10-cp310-cp310-win_amd64.whl`.
> Pour installer le package GDAL du projet et entrez la commande : 

```
pip install "flatcam\assets\Working GDAL-RASTERIO wheels\rasterio-1.2.10-cp310-cp310-win_amd64.whl"
```
Testez avec : 
```
python -c "import rasterio; print(rasterio.__version__)"
```


## Ouvrir FlatCAM en mode développement

Si toutes les étapes précédentes ont été suivies correctement et sans problème, vous pouvez à présent lancer FlatCAM depuis l'IDE.

Dans un terminal, rendez-vous à l'emplacement du "Dossier global" contenant l'ensemble du projet, c'est-à-dire `env310/` et `flatcam/`.

Lancez les commandes suivantes pour activer l'environnement virtuel, vous déplacer dans le projet et lancer flatcam:

```
.\env310\Scripts\activate
python .\flatcam\flatcam.py
```

---

→ Voir ensuite : [Code annoté](code-annote.md)
