# 📂 Dossier : Info3/ APP1

---

## 🔹 Modules à importer

| Module | Description |
|--------|-------------|
| [mcc_generated_files/system/system.h](## code-fonctions) | Initialise tous les modules et fonctions requises |
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
  // Clignoter à l'infini
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
        // Récuperation de l'état du BP
        bool etatBP = BP_GetValue();
        // Tant que bouton appuyé => clignoter
        while (!etatBP)
        {
            etatBP = BP_GetValue();
            LED_STOP_Toggle();
            LED_GAUCHE_Toggle();
            LED_DROITE_Toggle();
            __delay_ms(250);
        }
        // Forçage des LEDs à  0
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

    int8_t compteur = 0; // initialisation compteur

    while(1)
    {
        // Recupération état bouton
        bool etatBP = BP_GetValue();
        // Tant qu'on reste appuyé, lance 20 clignotements
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
        // Attente de relachement BP
        while (!etatBP)
        {
            etatBP = BP_GetValue();
        }
        compteur = 0;
        // Forçage de l'extinction des LEDs
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
    
    uint16_t val_pot1 = 0; // Valeur potentiomètre
    while(1)
    {
        // Récupération
        val_pot1 = ADCC_GetSingleConversion(POT);
        // Délai de 0.5s
        __delay_ms(500);
        // Affichage
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

    float val_pot1 = 0; // Valeur potentiomètre
    int16_t n = 10;     // Nombre clignotement
    float t = 0;        // secondes de clignotement
    
    while(1)
    {
        // Récuperation de l'état du bouton poussoir
        bool etatBP = BP_GetValue();
        if (!etatBP)
        {
            // Lance n clignotements
            for (int i=0; i<2*n; i++)
            {
                // récupération de la valeur POT
                val_pot1 = ADCC_GetSingleConversion(POT);
                // Faire en sorte que la durée d'allumage de la LED soit
                // entre 0 et 2000
                t = val_pot1*2000/1023;
                // Conversion en entier
                int T = (int)t;
                // Affichage
                printf("# Duree clignotement = %d ms # \n", 2*T);
                //Clignotement
                LED_STOP_Toggle();
                LED_GAUCHE_Toggle();
                LED_DROITE_Toggle();
                // Pause de T secondes
                for (int j = 0; j < T; j++) {
                    __delay_ms(1);
                }
            }
        }
    }
        
}
```
## Code Fonctions
```C
// Conversion des résultat du potentiomètre
adc_result_t ADCC_GetSingleConversion(adc_channel_t channel)
{
    // select the A/D channel
    ADPCH = channel ;

    // Turn on the ADC module
    ADCON0bits.ADON = 1 ;

    //Disable the continuous mode.
    ADCON0bits.ADCONT = 0 ;

    // Start the conversion
    ADCON0bits.ADGO = 1 ;

    // Wait for the conversion to finish
    while (ADCON0bits.ADGO)
    {
    }

    // Conversion finished, return the result
    return ((adc_result_t)((ADRESH << 8) + ADRESL)) ;
}
```
