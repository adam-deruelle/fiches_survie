
# 📂 Dossier : Info2 / APP4

---

## 🔹 Modules à importer

| Module | Description |
|--------|-------------|
| `board` | Accès aux broches du microcontrôleur |
| `pwmio` | Génération de signaux PWM |
| `digitalio` | Entrées/sorties numériques (ex : bouton-poussoir) |
| `displayio` | Affichage graphique (LCD) |
| `busio` | Pour les bus SPI / I2C |
| `adafruit_adxl34x` | Accéléromètre ADXL345 |
| `atan2` | Fonction mathématique pour roulis, tangage, lacet |

---

## 🛠️ Fonctions & classes utilisées

| Fonction / Classe | Rôle |
|-------------------|------|
| `busio.I2C(scl=GPXX, sda=GPXX)` | Initialise le bus I2C |
| `adafruit_adxl34x.ADXL345(i2c)` | Initialise le capteur |
| `digitalio.DigitalInOut(pin)` | Gère une broche numérique |
| `displayio.TileGrid` | Affiche des bitmaps en tuiles |
| `displayio.Group` | Regroupe des éléments graphiques |

---

## 📘 Tutos pratiques

### 🔹 Tuto 1 : Scanner les périphériques I2C

Créer un bus I2C et vérifier que la communication avec l’accéléromètre fonctionne.

```python
import board
import digitalio
import busio

# Création du bus I2C
busI2C = busio.I2C(scl=GP21, sda=20)

# Attendre que le bus I2C soit disponible pour le verrouiller
while not(busI2C.try_lock()):
    pass

# Récupération des adresses des circuits I2C
# Attention les adresses sont en décimal (il faut les convertir en hexa)
listAdresse = busI2C.scan()
listHexa = [hex(adresse) for adresse in listAdresse]

# on parcout la liste ListAdresse pour convertir chaque adresse en hexadécimal
# utiliser la fonction hex() de Python
print(f"Adresses I2C détectées : {listHexa}")

# on libère le bus I2C
busI2C.unlock()
```

---

### 🔹 Tuto 2 : Mesurer l’accélération

Affichage dans la console graphique (Mu-Editor).

```python
import board
import digitalio
import busio
import time
import adafruit_adxl34x

# Création du bus I2C
busI2C = busio.I2C(scl=GP21, sda=20)

# Instanciation de l'objet accelerometre
accelerometre = adafruit_adxl34x.ADXL345(busI2C)

# Attendre que le bus I2C soit disponible pour le verrouiller
while not(busI2C.try_lock()):
    pass

while True:
# Acquisition des valeurs
    acc_x, acc_y, acc_z = accelerometre.acceleration
    print((acc_x, acc_y, acc_z))
    time.sleep(1)

# on libère le bus I2C
busI2C.unlock()
```

---

### 🔹 Tuto 3 : Affichage des valeurs brutes

Accès aux données directement fournies par le capteur.

```python
# Rajouter dans la boucle principale
print(f"x : {accelerometre.raw_x} | y : {accelerometre.raw_y} | z : {accelerometre.raw_z}")
```

---

### 🔹 Tuto 4 : Calibration de l’accéléromètre

Positionner le capteur dans une position connue.

```python
## Rajouter avant la boucle principale

# Bouton Y
swy = digitalio.DigitalInOut(board.GP15)
swy.direction = digitalio.Direction.INPUT
swy.pull = digitalio.Pull.UP

# Reinitialiser les valeurs deja présente
accelerometre.offset(0, 0, 0)

print("Appuyer sur Y pour calibrer")

# Attente de l'appui
while swy.value:
    pass

# Création des variables
ax_off = ay_off = az_off = 0

# Acquisition des valeurs
for i in range(10):
    ax_off += accelerometre.raw_x
    ay_off += accelerometre.raw_y
    az_off += accelerometre.raw_z

# Moyennage
ax_off /= 10
ay_off /= 10
az_off /= 10

# Calcul des valeurs
xOffset = -round(ax_off / 4.138)
yOffset = -round(ay_off / 4.138)
zOffset = -round((az_off - 256) / 4)

# Modification des valeurs dans l'accelerometre
accelerometre.offset(xOffset, yOffset, zOffset)

# Ecriture dans le processeur
time.sleep(0.2)

# Affichage
print(f"x : {ax_off} | y : {ay_off} | z : {az_off}")
print("Appuyer sur Y pour terminer la calibration")

# Attente d'appui sur le bouton
while swy.value:
    pass
```

---

### 🔹 Tuto 5 : Roulis et tangage

Calculs à partir des composantes de l’accélération.

```python
# Importation du module
from math import atan2

# Rajouter dans la boucle principale

while True:
    ...
    alpha = atan2(acc_z / acc_y)
    teta = atan2(acc_z / acc_x)
    print(f"Roulis : {alpha}° | Tangage : {teta}°")
```
