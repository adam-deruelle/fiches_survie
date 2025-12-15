
# 🌟 Table des Matières

- [🚀 APP 1](#-dossier--info3-app1)
- [👨‍🚀 APP 2](#-dossier--info3-app2)

---


# 📂 Dossier : Info3/ APP1


## 🔹 🪄 Modules à importer 🪄

| Module | Description |
|--------|-------------|
| `mcc_generated_files/system/system.h`| Initialise tous les modules et fonctions requises |
| `stdint.h` | Permet de choisir le nombre de bit alloués |
---

## 🛠️ Fonctions & classes utilisées 🛠️

| Fonction / Classe | Rôle |
|-------------------|------|
| [`adc_result_t ADCC_GetSingleConversion(adc_channel_t channel)`](#code-fonctions-app1) | Convertit les valeurs du potentiomètre en int |
---

## 📘 Tutos pratiques

### 🔹 Tuto 1 : Premiers pas sur MCC 👣

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

### 🔹 Tuto 2 : Amélioration pour un usage manuel 📈

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

### 🔹 Tuto 3 : Tâche trop répitive ? Compteur 🔢

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

### 🔹 Tuto 4 : Affichage stylé 😎

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

### 🔹 Tuto 5 : Trop rapide ? Trop lent ? Choisissez ! ⏲

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
## Code Fonctions APP1 🛠️
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
## Allons plus loin 🚀
---

# 📂 Dossier : Info3/ APP2

---

## 🔹 🪄 Modules à importer 🪄

| Module | Description |
|--------|-------------|
| `mcc_generated_files/system/system.h`| Initialise tous les modules et fonctions requises |
| `stdint.h` | Permet de choisir le nombre de bit alloués |
---

## 🛠️ Fonctions & classes utilisées 🛠️

| Fonction / Classe | Rôle |
|-------------------|------|
| [`float Conversion_tension_distance(float tension)`](#code-fonctions-app2) | Convertit les valeurs du télémètre en tension|

---
### 🔹 Tuto 1 : Pas la bonne échelle ? 🪜

Acquisition de la valeur du télémètre et retour de sa valeur entre 0 et 5V.

```C

int main(void)
{
    SYSTEM_Initialize();

    // Initialisation des cariables
    int16_t val_tel = 0;
    float val_v = 0;
    
    while(1)
    {
        // Récupération de la valeur du télémètre
        val_tel = ADC_ChannelSelectAndConvert(TELEMETRE);
        // Retour de la valeur entre 0 et 5
        val_v = (val_tel*5)/1023.0 ;
        __delay_ms(100);

        // Affichage
        printf("# %d # \n", val_tel);
        printf("# %.3f # \n", val_v);
    }  
}
```
---
### 🔹 Tuto 2 : Attention devant ! 💥🚗

Détection d'obstacles.

```C
int main(void)
{
    SYSTEM_Initialize();

    // Initialisation des variables
    int16_t val_tel = 0;
    float val_v = 0;
    int16_t telmax = 400;
    
    while(1)
    {
        // Acquisition de la valeur du télémètre
        val_tel = ADC_ChannelSelectAndConvert(TELEMETRE);
        __delay_ms(50);

        // Affichage de la valeur
        printf("# %d # \n", val_tel);
        // Cas où il y a un obstacle à moins de 40cm
        if (val_tel < telmax) 
        {
            LED_D_Toggle();
            LED_G_Toggle();
            LED_STOP_SetLow();
        }
        // Cas où il n'y a plus d'obstacle
        if (val_tel >= telmax) 
        {
            LED_D_SetLow();
            LED_G_SetLow();
            LED_STOP_SetHigh();
        }
    }  
}
```
---
### 🔹 Tuto 3 : Méthode N°2 pour obetnir la distance 🥈

Grâce à une série de mesures on trouve une droite de tendence (Excel ou Calc) et on linéarise.
```C
// Fonction pour convertir la distance grâce à la linéarisation
float Conversion_tension_distance(float tension);

int main(void)
{
    SYSTEM_Initialize();

    // Initialisation des variables    
    uint16_t val_v;
    float dist = 0 ;
    
    while(1)
    {
        // Choisir le PIN du télémètre
        ADC_ChannelSelect(TELEMETRE);
        // Lancer la conversion 
        ADC_ConversionStart();
        // On attend que ça se termine
        while(!ADC_IsConversionDone()){

        }
        // Message de fin de conversion
        printf("### Conversion Done ! ###\n");
        // On récupére la valeur
        val_v = ADC_FilterValueGet();
        // On la ramène en tension
        float tension = ((float)val_v) * 5.0 / 1023.0;
        // On convertit grâce à notre fonction
        dist = Conversion_tension_distance(tension);
        __delay_ms(250);
        // Affichage
        printf("# %.1f cm # \n", dist);
    }  
}

```
---
### 🔹 Tuto 4 : Méthode N°3 pour obetnir la distance 🥉 et un petit bonus avec 🫣

Méthode qui utilise le module ADC pour obtenir la valeur du télémètre.


```C
int main(void)
{
    SYSTEM_Initialize();

    // Initialisation des variables
    int16_t val_pot = 0;
    int8_t etape = 0;
    float val_v = 0;
    
    while(1)
    {
        // Récupération et traitement de la valeur du Pot
        val_pot = ADC_ChannelSelectAndConvert(POT);
        etape = (val_pot*3)/1023 ;
        __delay_ms(50);

        // Affichage
        printf("# %d # \n", val_pot);

        // Changement de cas
        switch (etape)
        {
            case 0:
                LED_STOP_SetLow();
                LED_G_SetLow();
                LED_D_SetLow();
                break;
            case 1:
                //LED 1
                LED_G_SetHigh();
                LED_D_SetLow();
                LED_STOP_SetLow();
                break;
            case 2 :
                // LED 1 & 2
                LED_D_SetHigh();
                LED_G_SetHigh();
                LED_STOP_SetLow();
                break;
            case 3 :
                // LED Stop
                LED_D_SetHigh();
                LED_G_SetHigh();
                LED_STOP_SetHigh();
                break;
        }
                
    }  
}
```

---
### 🔹 Tuto 5 : En cours de développement... 🏗️

Clignotement de LED spécifique en fonction de la distance de l'obstacle.
**Bonus** : Affichage avancé.

```C
float Conversion_tension_distance(float tension);


int main(void)
{
    SYSTEM_Initialize();

    // Initialisation des variables
    uint16_t val_v;
    uint8_t graphique[] = "Obstacle : [                                      ]";
    float dist;
    int index_prec = 12;

    // Boucle principale
    while(1)
    {
        // Affichage
        graphique[index_prec] = ' ';
        ADC_ChannelSelect(TELEMETRE);
        ADC_ConversionStart();
        while(!ADC_IsConversionDone()){

        }
        val_v = ADC_FilterValueGet();
        float tension = ((float)val_v) * 5.0 / 1023.0;
        dist = Conversion_tension_distance(tension);
        int index = (int)dist + 12;
        graphique[index] = 'X';
        index_prec = index;
        __delay_ms(250);
    
        printf("%s\n", graphique);
        printf("%d\n", index);
    }  
}
```
--- 

## Code Fonctions APP2 🛠️
```C
// Conversion des résultat du télémètre
float Conversion_tension_distance(float tension) {
    float distance = 1/(0.098*tension + 0.014);
    return distance;
}
```

---

# Maintenant à vous de jouer 🫵
