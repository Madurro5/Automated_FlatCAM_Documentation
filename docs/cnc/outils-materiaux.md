# Outils & Matériaux

Cette page a pour but d'exposer les outils à disposition de la SETM pour usiner avec la CNC. Les fraises, forêts et différents types de pointe sont présentés ainsi que les formules et données permettant de faire les meillleurs choix paramétrage.

## Choix de l'outil

La SETM dispose de :

- FCCD200 : Fraise carbure coupe diamant 3.175/2.00/10/38
Fraise carbure coupe diamant Queue : 3.175 mm Diamètre de coupe : 2.00 mm Longueur de coupe : 10mm Longueur totale : 38 mm

- FCCD060 : Fraise carbure coupe diamant 3.175/2.00/06/4/38
Fraise carbure coupe diamant Queue : 3.175 mm Diamètre de coupe  : 0.6 mm Longueur de coupe : 4 mm Longueur totale : 38 mm

- PJ15 : Pointe carbure javelot 15° 0.2 mm
Pointe carbure javelot. Queue : 3.175 mm angle : 15° Diamètre de la pointe : 0.2 mm Longueur totale : 38 mm. Pour matériaux tendres, non ferreux.

- PJA30 : Pointe carbure javelot 30° 0.2 mm pour Aluminium, matériaux durs non ferreux.
Pointe carbure javelot. Queue : 3.175 mm Angle : 30° Diamètre de la pointe : 0.2 mm Longueur totale : 38 mm. Pour la gravure de l'aluminium, matériaux tendres, alliages, ...

- PJA45 : Pointe carbure javelot 45° 0.2 mm pour Aluminium, matériaux durs non ferreux.
Pointe carbure javelot. Queue : 3.175 mm Angle : 45° Diamètre de la pointe : 0.2 mm Longueur totale : 38 mm. Pour la gravure de l'aluminium, matériaux tendres, alliages, ...

- Fraise R2D baguée : Diamètre fraise 1,5 mm

- Fraise hélicoïdale : Diamètres disponibles {0.7 ; 0.8 ; 0.9 ; 1.0 ; 1.2 ; 1.5 ; 2.0 ; 3.0} mm

>**Note du fournisseur :** "PJM -> Mieux que PJA pour la gravure car pointe pyramidale = meilleure coupe dans l'epoxy (pas forcément vrai dans d'autres matières)"

## Matériaux usinables

## Paramètres de coupe CncFraises / Pour une fraie numérique type "Hobby" (vitesse / profondeur de passe)

fz (mm/dent) : Avance par dent
d : Diamètre de coupe de l'outil (d en mm)

| Matière | Vitesse de coupe (vc m/min) | fz pour d ≥1 | fz pour d ≥2 | fz pour d ≥3 | fz pour d ≥4 | fz pour d ≥5 | fz pour d ≥6 | fz pour d ≥8 |
|---|---|---|---|---|---|---|---|---|
| Bois, CTP | 500 | 0.025 | 0.030 | 0.035 | 0.060 | 0.070 | 0.090 | 0.10 |
| Bois dur | 450 | 0.020 | 0.025 | 0.030 | 0.055 | 0.065 | 0.085 | 0.095 |
| MDF | 450 | 0.050 | 0.070 | 0.100 | 0.150 | 0.200 | 0.300 | 0.400 |
| PVC expansé (Forex, Komacel) | 300 | 0.040 | 0.060 | 0.15 | 0.200 | 0.250 | 0.350 | 0.400 |
| PMMA, PC, POM, ... | 250 | 0.015 | 0.020 | 0.025 | 0.050 | 0.060 | 0.080 | 0.090 |
| Aluminium (2017A, 5083, ...) | 100–150 | 0.010 | 0.010 | 0.010 | 0.015 | 0.015 | 0.020 | 0.030 |
| Bronze, Cuivre, Laiton | 150–300 | 0.010 | 0.010 | 0.020 | 0.025 | 0.030 | 0.040 | 0.050 |
| Acier doux | 80 | 0.010 | 0.010 | 0.010 | 0.020 | 0.030 | 0.035 | 0.045 |


### Notations

- n = vitesse de rotation de l'outil (en tr/min) 
- d = diamètre de coupe de l'outil (en mm) 
- Z = nombre de dents 
- vc = vitesse de coupe (en mm/min) 
- fz = avance par dent (mm/dent) 
- vf = avance en matière (mm/min)

### **Etape 1 :** Détermination de la vitesse de rotation de l'outil "n"

Formule : n = (1000 * vc) / π * d 
Exemple pour aluminium vs= 200 dans le tableau et une fraise d'un diamètre de coupe de 3mm : 
n= (1000 * 200)/(3.14*3), n=21231 tr/min 

### **Etape 2 :** Détermination de l'avance en matière "vf"

Formule : vf = n*fz*Z 
Exemple pour aluminium, fraise 1 dent de 3 mm, "fz" du tableau et "n" calculé précédemment : 
vf=21231* 0.010 * 1, vf=212.31mm/min

> Ces valeurs sont à corréler avec la config GRBL et les paramètres FlatCAM.  
> → Voir : [Guide Avancé FlatCAM](../chaine-logicielle/automated-flatcam/guide-avance.md)
