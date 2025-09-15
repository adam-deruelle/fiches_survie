# 📂 Dossier : Info3/ APP1

---

## 🔹 Modules à importer

| Module | Description |
|--------|-------------|
| `mcc_generated_files/system/system.h` | Initialise tous les modules et fonctions requises |
| `stdint.h` | Permet de choisir le nombre de bit alloués |
---

## 🛠️ Fonctions & classes utilisées

| Fonction / Classe | Rôle |
|-------------------|------|
| `adc_result_t ADCC_GetSingleConversion(adc_channel_t channel)` | Convertit les valeurs du potentiomètre en int |
---

## 📘 Tutos pratiques

### 🔹 Tuto 1 : Scanner les périphériques I2C

Faire clignoter une LED.

```C
#include "mcc_generated_files/system/system.h"

int main(void)
{
  SYSTEM_Initialize();
  while(1)
  {
      LED_STOP_Toggle();
      LED_GAUCHE_Toggle();
      LED_DROITE_Toggle();
      __delay_ms(250);
      printf("\t8===D \n");
  }    
```

---

### 🔹 Tuto 2 : Mesurer l’accélération

Clignoter une LED tant que bouton poussoir pressé.

```C
#include "mcc_generated_files/system/system.h"

int main(void)
{
    SYSTEM_Initialize();

    while(1)
    {
        bool etatBP = BP_GetValue();
        while (!etatBP)
        {
            etatBP = BP_GetValue();
            LED_STOP_Toggle();
            LED_GAUCHE_Toggle();
            LED_DROITE_Toggle();
            __delay_ms(250);
            printf("\t8===D \n");
            printf("%d", etatBP);
        } 
        LED_STOP_SetLow();
        LED_GAUCHE_SetLow();
        LED_DROITE_SetLow();
    }    
}
```

---

### 🔹 Tuto 3 : Affichage des valeurs brutes

Par appuit BP faire clignoter x fois (utilisation compteur).

```C
#include "mcc_generated_files/system/system.h"
#include <stdint.h>

int main(void)
{
    SYSTEM_Initialize();

    int8_t compteur = 0;

    while(1)
    {
        bool etatBP = BP_GetValue();
        while (!etatBP && compteur < 20)
        {
            etatBP = BP_GetValue();
            LED_STOP_Toggle();
            LED_GAUCHE_Toggle();
            LED_DROITE_Toggle();
            __delay_ms(250);
            printf("\t8===D \n");
            printf("%d", compteur);
            compteur++;
        } 
        while (!etatBP)
        {
            etatBP = BP_GetValue();
        }
        compteur = 0;
        LED_STOP_SetLow();
        LED_GAUCHE_SetLow();
        LED_DROITE_SetLow();
    }    
}
```

---

### 🔹 Tuto 4 : Calibration de l’accéléromètre

Afficher la valeur du potentiomètre (2 acquisitions par secondes).

```C
#include "mcc_generated_files/system/system.h"
#include <stdint.h>

adc_result_t ADCC_GetSingleConversion(adc_channel_t channel);

int main(void)
{
    SYSTEM_Initialize();
    
    uint16_t val_pot1 = 0; 
    while(1)
    {
        val_pot1 = ADCC_GetSingleConversion(POT);
        __delay_ms(500);
        printf("# %d # \n", val_pot1);
    }
        
}
```

---

### 🔹 Tuto 5 : Roulis et tangage

Clignoter des LEDs en modulant la durée grâce au potentiomètre.

```C
#include "mcc_generated_files/system/system.h"
#include <stdint.h>

adc_result_t ADCC_GetSingleConversion(adc_channel_t channel);

int main(void)
{
    SYSTEM_Initialize();

    float val_pot1 = 0; 
    int16_t n = 10;
    float t = 0;
    
    while(1)
    {
        bool etatBP = BP_GetValue();
        if (!etatBP)
        {
            for (int i=0; i<2*n; i++)
            {
                val_pot1 = ADCC_GetSingleConversion(POT);
                t = val_pot1*2000/1023;
                int T = (int)t;
                printf("# Duree clignotement = %d ms # \n", 2*T);
                LED_STOP_Toggle();
                LED_GAUCHE_Toggle();
                LED_DROITE_Toggle();
                for (int j = 0; j < T; j++) {
                    __delay_ms(1);
                }
            }
        }
    }
        
}
```
