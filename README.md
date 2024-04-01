# Interrupts in PIC Microcontrollers:
_Interrupts are a crucial feature in microcontrollers like the PIC16F887. They allow the microcontroller to respond to external events or internal conditions promptly, without having to continuously poll for them. This improves efficiency and responsiveness in embedded systems._

# _Types of Interrupts:_

### _1- External Interrupts:_ These interrupts are triggered by external events such as a change in voltage level on a pin, a timer overflow, or a signal from another device. The PIC16F887 has multiple external interrupt pins (e.g., RB0/INT, RB7/INT0) that can be configured to trigger interrupts.

### _2- Timer Interrupts:_ The microcontroller's built-in timers can generate interrupts when they overflow or match a specific value. This feature is useful for tasks that require precise timing, such as generating PWM signals or measuring time intervals.

### _3- Peripheral Interrupts:_ Some peripherals within the microcontroller, such as the USART (serial communication module) or the ADC (analog-to-digital converter), can generate interrupts to signal the completion of a data transfer or conversion.

# _Interrupt Service Routine (ISR):_

### _When an interrupt occurs, the microcontroller suspends its current execution and jumps to a predefined Interrupt Service Routine (ISR) to handle the interrupt. The ISR performs necessary tasks related to the interrupt, such as reading data from sensors, updating variables, or responding to external commands._

# _Interrupt Priority:_

### _In PIC microcontrollers, interrupts can have different priority levels. This allows the programmer to specify which interrupts should be serviced first if multiple interrupts occur simultaneously. Priority levels can be configured in the microcontroller's interrupt control registers._

# _Interrupt Configuration:_

_To use interrupts in a PIC microcontroller like the PIC16F887, the following steps are typically involved:_
#### 1- Configure the specific interrupt source (e.g., external pin, timer, peripheral).
#### 2- Enable global interrupts and set the interrupt priority, if necessary.
#### 3- Write the ISR to handle the interrupt.
#### 4- Clear the interrupt flag in the ISR to acknowledge the interrupt and allow subsequent interrupts of the same type to occur.

# _Example:_

```Assembly
    list    p=16F887           ; list directive to define processor
    #include <p16F887.inc>     ; processor specific variable definitions

    __CONFIG _CONFIG1, _CP_OFF & _CCP1_RB0 & _DEBUG_OFF & _WRT_OFF & _CPD_OFF & _LVP_OFF & _CP_OFF & _BODEN_ON & _MCLRE_ON & _PWRTE_ON & _WDT_OFF & _INTRC_OSC_NOCLKOUT
    __CONFIG _CONFIG2, _IESO_OFF & _FCMEN_OFF
    
    ; Register definitions
    LED_PIN     equ RB3            ; Define LED pin
    INT_PIN     equ RB0            ; Define external interrupt pin

    ; Interrupt vector
    org 0x0004                      ; Interrupt vector location
    goto ISR                        ; Jump to ISR when interrupt occurs

    ; Interrupt Service Routine (ISR)
    ISR:
        btfss   INTCON, INTF       ; Check if RB0/INT interrupt flag is set
        return                     ; If not set, return from ISR
        ; Toggle LED pin
        bcf     INTCON, INTF       ; Clear RB0/INT interrupt flag
        movlw   0x01
        xorwf   LED_PIN, f         ; Toggle LED pin
        retfie                      ; Return from interrupt, enable interrupts

    ; Main program
    org 0x0000                      ; Program start address
    goto Main                       ; Jump to main program

    Main:
        bsf     STATUS, RP0        ; Bank 1
        movlw   0x00
        movwf   TRISB              ; Set PORTB as output
        bcf     STATUS, RP0        ; Bank 0
        bcf     INTCON, INTE       ; Disable RB0/INT external interrupt
        bsf     INTCON, INTEDG     ; Interrupt on falling edge of RB0/INT
        bsf     INTCON, GIE        ; Enable global interrupts
        bcf     INTCON, PEIE       ; Disable peripheral interrupts

    Loop:
        nop                         ; No operation, or other main program instructions
        goto    Loop                ; Endless loop

    end                             ; End of program

```

# _Here's a simple flowchart illustrating the main program loop (prog.principal) in the assembly code_:

```Assembly
                                        +---------------------------------------+
                                        |             Start Program             |
                                        +---------------------------------------+
                                        |                                       |
                                        |              Initialize               |
                                        |           Configuration               |
                                        |               Bits                    |
                                        |                                       |
                                        +---------------------------------------+
                                        |             Main Loop                 |
                                        +---------------------------------------+
                                        |                                       |
                                        |       Check for RB0/INT Interrupt     |
                                        |                                       |
                                        |       ┌───────────────────────┐       |
                                        |       │   Interrupt Triggered  │       |
                                        |       │      (INTF set)       │       |
                                        |       └───────────────────────┘       |
                                        |                 │                   |
                                        |       ┌─────────┴─────────┐         |
                                        |       │    Toggle LED     │         |
                                        |       └───────────────────┘         |
                                        |                 │                   |
                                        |       ┌─────────┴─────────┐         |
                                        |       │  Clear INTF Flag  │         |
                                        |       └───────────────────┘         |
                                        |                 │                   |
                                        |       ┌─────────┴─────────┐         |
                                        |       │   Return from ISR  │         |
                                        |       └───────────────────┘         |
                                        |                                       |
                                        +---------------------------------------+
                                        |             End Program               |
                                        +---------------------------------------+
```













