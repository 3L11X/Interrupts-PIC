# Interruptions dans les microcontrôleurs PIC (PIC16F887) :

_Les interruptions sont une fonctionnalité cruciale dans les microcontrôleurs comme le PIC16F887. Elles permettent au microcontrôleur de répondre rapidement à des événements externes ou des conditions internes, sans avoir à les sonder en continu. Cela améliore l'efficacité et la réactivité dans les systèmes embarqués._

# _Types d'interruptions :_

### _1- Interruptions externes :_ Ces interruptions sont déclenchées par des événements externes tels qu'un changement de niveau de tension sur une broche, un débordement de temporisation ou un signal provenant d'un autre dispositif. Le PIC16F887 dispose de plusieurs broches d'interruption externes (par exemple, RB0/INT, RB7/INT0) qui peuvent être configurées pour déclencher des interruptions.

### _2- Interruptions de temporisation :_ Les temporisateurs intégrés du microcontrôleur peuvent générer des interruptions lorsqu'ils débordent ou correspondent à une valeur spécifique. Cette fonctionnalité est utile pour les tâches nécessitant une synchronisation précise, telles que la génération de signaux PWM ou la mesure d'intervalles de temps.

### _3- Interruptions de périphérique :_ Certains périphériques à l'intérieur du microcontrôleur, tels que l'USART (module de communication série) ou le CNA (convertisseur analogique-numérique), peuvent générer des interruptions pour signaler l'achèvement d'un transfert de données ou d'une conversion.

# _Routine de service d'interruption (RSI) :_

### _Lorsqu'une interruption se produit, le microcontrôleur suspend son exécution actuelle et saute à une Routine de Service d'Interruption (RSI) prédéfinie pour gérer l'interruption. La RSI effectue des tâches nécessaires liées à l'interruption, telles que la lecture de données à partir de capteurs, la mise à jour de variables ou la réponse à des commandes externes._

# _Priorité des interruptions :_

### _Dans les microcontrôleurs PIC, les interruptions peuvent avoir différents niveaux de priorité. Cela permet au programmeur de spécifier quelles interruptions doivent être traitées en premier si plusieurs interruptions se produisent simultanément. Les niveaux de priorité peuvent être configurés dans les registres de contrôle des interruptions du microcontrôleur._

# _Configuration des interruptions :_

_Pour utiliser des interruptions dans un microcontrôleur PIC comme le PIC16F887, les étapes suivantes sont généralement nécessaires:_

#### 1- Configurer la source d'interruption spécifique (par exemple, broche externe, temporisateur, périphérique).
#### 2- Activer les interruptions globales et définir la priorité des interruptions, si nécessaire.
#### 3- Écrire la RSI pour gérer l'interruption.
#### 4- Effacer le drapeau d'interruption dans la RSI pour reconnaître l'interruption et permettre aux interruptions ultérieures du même type de se produire.

# _Exemple :_

```Assembly
    list    p=16F887           ; directive de liste pour définir le processeur
    #include <p16F887.inc>     ; définitions de variables spécifiques au processeur

    __CONFIG _CONFIG1, _CP_OFF & _CCP1_RB0 & _DEBUG_OFF & _WRT_OFF & _CPD_OFF & _LVP_OFF & _CP_OFF & _BODEN_ON & _MCLRE_ON & _PWRTE_ON & _WDT_OFF & _INTRC_OSC_NOCLKOUT
    __CONFIG _CONFIG2, _IESO_OFF & _FCMEN_OFF
    
    ; Définitions des registres
    LED_PIN     equ RB3            ; Définir la broche de LED
    INT_PIN     equ RB0            ; Définir la broche d'interruption externe

    ; Vecteur d'interruption
    org 0x0004                      ; Emplacement du vecteur d'interruption
    goto ISR                        ; Sauter à la RSI lorsqu'une interruption se produit

    ; Routine de service d'interruption (RSI)
    ISR:
        btfss   INTCON, INTF       ; Vérifier si le drapeau d'interruption RB0/INT est activé
        return                     ; Si non activé, retourner de la RSI
        ; Basculez la broche de LED
        bcf     INTCON, INTF       ; Effacer le drapeau d'interruption RB0/INT
        movlw   0x01
        xorwf   LED_PIN, f         ; Basculez la broche de LED
        retfie                      ; Retour de l'interruption, activer les interruptions

    ; Programme principal
    org 0x0000                      ; Adresse de démarrage du programme
    goto Main                       ; Sauter au programme principal

    Main:
        bsf     STATUS, RP0        ; Banque 1
        movlw   0x00
        movwf   TRISB              ; Définir PORTB comme sortie
        bcf     STATUS, RP0        ; Banque 0
        bcf     INTCON, INTE       ; Désactiver l'interruption externe RB0/INT
        bsf     INTCON, INTEDG     ; Interruption sur le front descendant de RB0/INT
        bsf     INTCON, GIE        ; Activer les interruptions globales
        bcf     INTCON, PEIE       ; Désactiver les interruptions périphériques

    Loop:
        nop                         ; Aucune opération, ou autres instructions du programme principal
        goto    Loop                ; Boucle infinie

    end                             ; Fin du programme
```

```Assembly
                    +---------------------------------------+
                    |         Début du Programme            |
                    +---------------------------------------+
                    |                                       |
                    |          Initialisation               |
                    |           Configuration               |
                    |               Bits                    |
                    |                                       |
                    +---------------------------------------+
                    |          Boucle Principale            |
                    +---------------------------------------+
                    |                                       |
                    |     Vérifier l'Interruption RB0/INT   |
                    |                                       |
                    |       ┌───────────────────────┐       |
                    |       │   Interrupt Triggered │      |
                    |       │      (INTF set)       │       |
                    |       └───────────────────────┘       |
                    |                 │                     |
                    |       ┌─────────┴─────────┐          |
                    |       │    Basculez LED     │          |
                    |       └───────────────────┘          |
                    |                 │                    |
                    |       ┌─────────┴─────────┐          |
                    |       │  Effacez le INTF  │          |
                    |       └───────────────────┘          |
                    |                 │                    |
                    |       ┌─────────┴─────────┐          |
                    |       │ Retour de la RSI  │          |
                    |       └───────────────────┘          |
                    |                                      |
                    +--------------------------------------+
                    |             Fin du Programme         |
                    +--------------------------------------+
```

# By ALI RCHAM (3L11X) AND RAYAN BOUMEHDI (SRA9ZIT)
