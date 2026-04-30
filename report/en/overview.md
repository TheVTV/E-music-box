# Melody Player from EEPROM Memory (Electronic Music Box)

**Author:** Bartosz Wójcik

## General Concept

I would like to build a melody player on prototype boards that stores melodies in "note format" in EEPROM memory. For this purpose, I would like to use the most basic electronic circuits, such as logic gates, counters, and flip-flops.

## Structure

*(Underlined items indicate functionalities/changes that arose during project execution)*

The system can be divided into two parts:
### 1. Note Reader from Memory

### 2. Sound Generator

## Note Reader from Memory (1)

Clock -> counter circuit -> EEPROM memory -> note decoder -> NPN transistor

In the memory circuit, the melody would be in an example format:

00000 - note C4  
00001 - note C#4  
00010 - note D4  
…

01011 - note B4  
10000 - note C5 (higher octave)  
10001 - note C#5  
10010 - note D5

…

11011 - note B5  
11111 - STOP bit  
01111 - PAUSE bit (pause between notes)

(so successive addresses are successive notes, for each address the most significant bit symbolizes the octave number, and the remaining 4 - the note)

I treat the signal as: O N4 N3 N2 N1 (where O - octave, N4-1 - encoded note)

The memory itself would work in parallel mode - 8 input pins, 5 output pins, so the output from the counters connected to the EEPROM input would generate a signal which we would later interpret as notes (frequency)

The "decoder" can be divided into two parts: note decoder and octave decoder:

The note decoder would have 4 inputs and 13 outputs (12 notes + STOP/PAUSE bit - the bit indicating the octave does not need to be considered, as it speaks for itself).

The octave decoder would have 13 inputs and 26 outputs (2 * 12 notes - two octaves + STOP bit + PAUSE bit). It is simple in that it is just an AND with bit O - octave 5, AND with negated bit O - octave 4.

I would like to build this entire decoder using gates.

## Sound Generator (2)

NPN transistor -> resistor -> potentiometer -> clock -> NPN transistor -> speaker/buzzer

The clock frequency (I chose the NE555 circuit) depends on R1, R2 and C, in this case R1 and C will be constant and the clock frequency will depend on R2. For each note output from the decoder there will be a separate resistor circuit serving as R2, so for each signal we will get a different frequency. To get a clean sound for a given note, its exact frequency is needed, and to obtain it we need resistors of exact "specific value", so to make work easier for each bundle I will add a 10k potentiometer + selected resistor to be able to "tune" the sound.

The clock generates a square wave, which through the transistor causes the speaker membrane to move and we generate sound. Perhaps it will be possible to create another signal, e.g. a sine wave.

### Example clock cycle to generate D4 sound

- Clock (generates square wave at specified frequency)
- Counter (switches to next value, let's say it now has 00000)
- EEPROM (reads what it has at address 00000, let's say 00010)
- Decoder (gets 0010 and transforms to D)
- AND the decoder output with bit O and we get D4
- Turn on the transistor which changes the resistor circuit to the one corresponding to D4 sound
- Clock generates the specified frequency and outputs the signal to the transistor
- Transistor turns speaker power on and off generating sound

## Additions

For complete happiness and "more pleasant" work, it would be worth additionally:

- EEPROM Programmer - technically you can buy one, but I'm going with the assumption "why buy when you can make it yourself". For this purpose, you can use a microcontroller, I would use ESP32 (because I have them on hand). The only problem is the voltage difference - esp works at 3.3V, while EEPROM (as far as I remember) works at 5V, but I'll manage somehow. Ideally, I would like to build this circuit not on a breadboard, but on a universal PCB board - such a circuit would not be disposable. Ultimately, to avoid dealing with registers and a small number of pins, I decided to use Arduino MEGA.

- Square wave generator - intended purely for debugging use. The clock could be set to monostable mode and single square wave signals could be sent (for debugging e.g. counters, reading from EEPROM, sound tuning). I also plan to solder the circuit on a universal PCB board.

- Frequency meter - I haven't delved into the circuit, so I don't know how complicated it would be. It would be useful for precise sound tuning, so I don't have to do it "by ear" or using a phone app, but maybe I'm overthinking it and it won't be necessary. The sound doesn't have to be tuned perfectly to the hundredth of a Hertz, and the app handles it very well - I'm abandoning the idea (and it's even better, I just bought myself a universal meter with frequency measurement capability)