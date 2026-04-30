# Week 1. (3-10.03.2026)

I started by carefully writing down what I wanted to do, to make it easier to design, but also to know what to buy. After initially pouring out the idea on paper I had a list of needed components, and I went shopping. The only questionable thing was the decoder itself, but first I wanted to design it to know how many and what kind of circuits to buy.

Initially, I thought a lot about how best to model the note decoder, namely so that I wouldn't use four-input ANDs and thus wouldn't do it "crudely". I came up with the idea of grouping the signals:

_O N4 N3 N2 N1 - note scheme (O - symbolizes the octave, N4-1 - 4-bit number from 0 to 13 symbolizing a note or STOP/PAUSE signal)_

_N4 N3 - 4 possible combinations (00, 01, 10, 11), I treat as group number_

_N2 N1 - also 4 possible combinations (00, 01, 10, 11), I treat as note number in the group_

This way I check which of the 4 groups we are in and which of the 4 notes from that group we are playing. Then I AND the group signal and the note and I know which note I have to play. For each note we need 3 AND gates + possibly NOT, but since I'm using the above grouping - I don't have to create separate ANDs for the group and note in the group for each note!

One AND is needed for each group - that's 4 gates + 2 NOT (to check for signal 0), similarly for each of the 4 notes in the group. So just for grouping I need 8 ANDs and 4 NOTs. Later I combine these outputs into successive AND gates and check which note I get (i.e. if N4 N3 is 00 (group 0), and N2 N1 is 00 (0 note in group) that means I'm playing sound C). Ultimately I need:

- 4 AND + 2 NOT (Group)
- 4 AND + 2 NOT (Note in group)
- 13 AND (For each note + STOP/PAUSE signal)

In total 21 AND gates and 4 NOT gates.

To not forget the idea and to be able to present it simply to the instructor, I sketched a preliminary note decoder circuit:

<div align="center">
  <img src="../../photos/week1/week1_sketch1.jpg" width="60%" alt="Note decoder sketch">
</div>

And here's how the circuit looks in Multisim:

<div align="center">
  <img src="../../photos/week1/week1_multi1.png" width="60%" alt="Multisim - note decoder">
</div>

As for the octave decoder, it works very simply. For each note from the previous decoder:

- if O gives signal 0 - it's octave 4
- if O gives signal 1 - it's octave 5

The implementation is trivial - two AND gates, to each I connect the signal for the given note and to one the octave signal, to the other - the negated octave signal. So if the signal comes out of the first gate, it will be a sound from octave 5, and if the signal comes out of the second gate - it will be a sound from octave 4.

I also sketched this idea so it wouldn't escape from my head.

<div align="center">
  <img src="../../photos/week1/week1_sketch2.jpg" width="60%" alt="Octave decoder sketch">
</div>

And this has to be repeated 13 times - for each of the 12 notes in the octave and additionally to separate the STOP and PAUSE signals.

And here's how the circuit looks in Multisim (here without STOP and PAUSE):

<div align="center">
  <img src="../../photos/week1/week1_multi2.png" width="60%" alt="Multisim - octave decoder">
</div>

In this case I get 26 AND gates and 1 NOT gate.

Using the fact that I have access to Multisim, I will connect both decoders and check the solution works:

<div align="center">
  <img src="../../photos/week1/week1_multi3.png" width="60%" alt="Multisim - decoders combined">
</div>

I use a word generator, which contains a sequence going through notes in order C4, Cis4 … Ais5, B5, STOP. So I run the simulation:

<div align="center">
  <img src="../../photos/week1/week1_multi4.png" width="60%" alt="Multisim - simulation">
</div>

And it works! The sounds go in order, which can be seen on the test probes. I noticed one problem - when changing from B4 to C5, between the probe B4 turning off and the probe C5 turning on, the probe B5 also turns on for a fraction of a second. Similarly the other way around - when changing from B5 to C4, the probe B4 flashes for a moment. This very much intrigued me, because this is the only place where this happens. I noticed that this happens when changing octaves - so I'll add a few more notes to the word generator so that the notes alternately are in different octaves. After testing I'm sure my suspicions were correct - the probes vary when changing octaves. I'm not sure what's causing this, the circuits themselves seem correct - they're so simple that you could say - impossible to break. Yet something here doesn't add up, I need to think about what could be wrong…

My thoughts:

- Multisim simply does delays and doesn't run the simulation correctly (which may be caused by poor laptop performance)
- The word generator does delays and changes the values of individual bits one by one, not all at once
- There's some problem in my circuit, but I have no idea what

My (most important) attempts at solutions:

- I eliminated the word generator idea - it changes all bits simultaneously. I connected the test probes directly to the word generator and they all change together
- I tried adding JK flip-flops on the outputs to update the state on the falling clock signal - that didn't help either
- I also tried connecting the decoders individually (to potentially find out which one has the problem) to the word generator. They worked without problems.

I have no idea what the problem might be. I read about it on the Internet and only found (but importantly) that the phenomenon is called logical hazard/glitch. Nice, new word to add to the dictionary ;)

For now I'm giving up, I spent too much time looking for the problem, and it's probably going to turn out to be one of the more trivial things… Either way, I'll bring this topic up at a consultation with the instructor, maybe together we can figure something out.

After the battle with the decoder in Multisim, I wanted to react a bit and start working on physical hardware, so I waited and waited for the component package. When the package arrived, I got right to work. First I wanted to play with counters, and for that I needed a clock signal. So I put an NE555 clock on the breadboard and set it to monostable mode to generate a square wave with a button. I connected an LED with a resistor to its output to check if everything works, and yes - the LED turned on when I pressed the button.

Next, I proceeded to read the documentation for the 4-bit counter circuit SN74HC161 to see how to wire it on the board:

<div align="center">
  <img src="../../photos/week1/week1_counter.png" width="20%" alt="SN74HC161 counter schematic">
</div>

This is a counter counting up with the ability to load a number at the beginning. The pins A, B, C, D and negated LOAD are used for this (so if a 0 is given to pin 9, the counter won't count, but will save the value from inputs A, B, C, D). Since I'm not using them, I connect pins 3-6 to ground, and pin 9 to VCC. I do the same with pin 1 (negated CLR), which will reset the counter when it receives state 0 - I connect it to VCC. Pin 2 is the clock input, so I connect it to the previously assembled circuit. Pins 7, 10 and 15 (ENP, ENT and RCO) are used to cascade counters and to enable counting. For now I'm playing with one counter so I connect pins 7 and 10 to VCC, and 15 to ground. The power pins remain, as well as pins 11-14, which are the counter output such that Qa is the least significant bit and Qd is the most significant bit. I connect LEDs with resistors to these outputs and observe how the counter works.

It works wonderfully, the LEDs flicker nicely, the counter counts, but why count from 0 to 15 when you can go from 0 to 255 ;)

Using the fact that I need more numbers for the final project, I equipped myself not with one, but two such counters. So I connect the second counter in practically the same way, but to make them count to 255 (not each to 15) they have to be connected together. That's what the ENT and RCO pins I mentioned earlier are for. When the counter reaches the value 15 (i.e. 1111, i.e. high state on all four output pins) the RCO (Ripple Carry Output) output changes its state from low to high. For the counter to count, pins ENP (Enable Parallel) and ENT (Enable T) must be high, and the counter must receive a rising clock signal. Using these properties, by connecting the RCO output of counter A to the ENT input of counter B "we will create" an 8-bit counter. I did just that, disconnecting pin 15 of counter A from ground and connecting it to pin 10 of counter B.

<div align="center">
  <img src="../../photos/week1/week1_countersTest1.jpg" width="60%" alt="Counter test - part 1">
</div>

<div align="center">
  <img src="../../photos/week1/week1_countersTest2.jpg" width="60%" alt="Counter test - part 2">
</div>

At this point our circuit is able to count from 0 to 255, which is sufficient for the start. However, it's monotonous to press the button to generate the clock signal, so I put another NE555 clock on the breadboard. This time, however, I set it to astable mode, so it generates the signal itself and not manually. For this, two resistors and a capacitor are needed. I don't want to go into a detailed description of the construction and operation of the NE555 circuit, but you should know that it generates a square wave, and the switching of the output (from 0 to 1 and back) depends on the voltage across the capacitor - when it reaches 2/3 VCC it switches to low state, and when it reaches 1/3 VCC it switches to high state. The capacitor itself is charged by R1 + R2, and discharged only by R2, so the charging and discharging times are different. The duration of each state can be determined by the formulas:

$$
t_{high} = 0.693(R_1 + R_2)C
$$

$$
t_{low} = 0.693(R_2)C
$$

Where R1, R2 - resistors, C - capacitor. The value 0.693 comes from the logarithmic nature of capacitor charging and discharging in an RC circuit. With both these times we are able to calculate the period:

$$
T = t_{high} + t_{low} = 0.693(R_1 + 2R_2)C
$$

And also the frequency:

$$
f = \frac{1}{T} \approx \frac{1.44}{(R_1 + 2R_2)C}
$$

That is, as you increase the resistance and capacitance values, the frequency decreases. Let's also consider the duty cycle, as it also plays an important role. This is the ratio of the duration of the high signal to the period:

$$
D = \frac{t_{high}}{T} = \frac{R_1 + R_2}{R_1 + 2R_2}
$$

That is, the duty cycle depends only on R1 and R2. I assume that to power the entire circuit I will use a voltage of ~5V, so I think a set of resistors with values R1=1k, R2=10k will be appropriate (both for power parameters and for duty cycle, which will be about 52%).

Let's calculate the capacitance needed to generate a signal with frequency of e.g. 1Hz

$$
1\text{ Hz} = \frac{1.44}{(1000\Omega + 2 \cdot 10000\Omega)C} \Rightarrow C \approx 68\,\mu\text{F}
$$

The largest capacitor I currently have on hand is 100nF, there's no point in connecting them. For now I'll put what I have to check if the circuit works, and eventually I'll replace it with a different capacitor.

The circuit works, although the LEDs flicker very fast, but I used (I think for the first time) the slow-motion recording feature on my phone to check if everything is counting as it should. I can initially determine - it works!

Let's move on to EEPROM memory. The one I'm using is the CAT28C64A, which stores 8192 8-bit numbers. It works in parallel mode, which is good for my project - I just need to supply the signal from the counters to the first 8 address lines, connect the rest to ground (so they are treated as 0), and I get an 8-bit number on the output which I can connect (for testing) to my LEDs. First, however, I need to deal with the programmer, because EEPROM memories are usually "zeroed" - all bits at all addresses are set to 1. I would only get 8 constantly lit LEDs, which would not only prevent me from testing the operation, but also wouldn't be very impressive.

So I took another breadboard and assembled the programmer!

<div align="center">
  <img src="../../photos/week1/week1_EEPROM.png" width="20%" alt="EEPROM Programmer">
</div>

To assemble the programmer I use Arduino MEGA which will help me a lot with programming the memory, due to the number of digital pins (I personally have an esp32, but not only does it have too few pins, it works at 3.3V. Fortunately my friend lent me an Arduino so I don't have to deal with registers and voltage differences). In summary, I connect almost all the pins from the EEPROM to the digital pins of the Arduino. Almost, because I connect pin 28 to VCC, pin 14 to ground, and pins 1 and 26 (NC - no connect) are left unconnected. I pay attention to 3 special pins - 20, 22 and 27 - because they are used to control the memory mode. Pin 20 is negated CE (Chip Enable), which when given 0 enables the circuit. Pin 22 is negated OE (Output Enable), which when given 0 switches the circuit to read mode. Pin 27 is negated WE (Write Enable), which when given 0 switches the circuit to write mode. Of course we can't allow a situation where both OE and WE pins have 0 at the same time (can't read and write at the same time, it could damage the circuit), so I decided to add "pull-up" resistors to both these pins and to CE. This way these pins will always be set to 1 by default, and if I want to enable a particular mode through Arduino, I just need to "pull" the current to ground programmatically. I use this because the time from board startup to the moment a value is set on a pin can be large enough that a situation arises where these pins are simultaneously set to low.

I wrote a simple program that reads the value in memory at a given address, writes a value to a given memory address, and prints the contents of the entire memory. This is done via READ, WRITE and DUMP commands (with appropriate arguments) on the board's serial port. Ultimately, I would also like to write a program in Python that will expand these functionalities by connecting to the board via the serial port, and will have the ability to, among other things, automatically save the contents of a file on the computer to EEPROM memory.

However, I noticed a downside to my programmer. Namely, with any operation on the memory I have to make sure that all wires are properly connected both to the Arduino and to the breadboard. Moreover, a slight movement of the circuit can cause a wire to become loose and as a result - values are written incorrectly. This is of course caused by the construction itself - the wires are not permanently inserted and can move, which is unacceptable in the longer term. That's why I need to solder this circuit on a universal PCB board - I think it will provide much better accuracy. At the moment I unfortunately don't have a DIP28 socket for easily removing and inserting memory into the circuit (it would be pointless to solder the EEPROM to the board), so I'm putting this off for later.

After uploading example values to memory, I return to the board with the counters and try to connect it all nicely together.

<div align="center">
  <img src="../../photos/week1/week1_EEPROM.png" width="20%" alt="EEPROM Programmer">
</div>

Pins A0-A12 are input lines. Since I have an 8-bit counter, I connect the counter outputs to lines A0-A7, and lines A8-A12 - to ground (they will then be treated as 0). Pins I/O_0 - I/O_7 are output lines, which I connect directly to the LEDs that were previously connected to the counters. The NC pins are still unconnected, VCC and VSS I connect respectively to VCC and ground, and as for the control pins - I connect CE and OE to ground (treated as 0), and WE - to VCC (treated as 1). This way the memory will always be set to read mode.

I run the circuit and check its operation:

<div align="center">
  <img src="../../photos/week1/week1_EEPROMTest1.jpg" width="60%" alt="EEPROM test - part 1">
</div>

<div align="center">
  <img src="../../photos/week1/week1_EEPROMTest2.jpg" width="60%" alt="EEPROM test - part 2">
</div>

As you can see, the circuit fulfills its task at this point. The counter on each clock cycle accesses the next memory cell, whose value is displayed on the LEDs. With intentional arrangement of values in memory, a nice light show can be created. However, (unfortunately) for me this is not enough, so I skip the part related to the note decoder and move on to the final part of the circuit, namely the sound generator.

For a long time, old headphones with a broken cable had been lying in my closet (in fact they inspired me to create this project), with a little "violence" I managed to get their speaker. After stripping the insulation from the wires and attaching male pins to them, it was already possible to plug it into the breadboard. So I took another breadboard in my hand and assembled an identical NE555 clock in astable mode, and then plugged the speaker into its signal output. After plugging in the power we have the first success - the speaker is alive and produces the characteristic sound of a square wave.

<div align="center">
  <img src="../../photos/week1/week1_generator1.jpg" width="60%" alt="Sound generator - version 1">
</div>

Two notes: the speaker crackles + plays too loud. So I added a precision potentiometer (only that one I have on hand) with a value of 10k and plugged it right before the speaker - this way I can control the volume. The crackling, however, persisted, so I decided to change the power supply to a lower one - from 5V to 3.3V - and the problem disappeared. I'll have to examine this closely, maybe it can be solved in a non-invasive way.

Everything is fine, but ultimately the speaker is supposed to produce a series of sounds, not just one. For this to happen, I need to focus not on the speaker itself, but on the generator. When the clock frequency is lower/higher, the sound frequency will be proportionally lower/higher.

The clock frequency can be adjusted through R1, R2 and C. It makes the most sense to fix one R1 and C, and change R2. So I remove the 10k resistor playing the role of R2 and replace it with a precision potentiometer 100k.

<div align="center">
  <img src="../../photos/week1/week1_generator2.jpg" width="60%" alt="Sound generator - version 2">
</div>

After connecting power and turning the potentiometer, the sound does indeed change. I even tried to tune the sound using an app on my phone, but with such a potentiometer you really need a steady hand - a small movement and the sound is impure. I think a better solution in the long run would be to install a regular resistor along with a smaller potentiometer instead of one large potentiometer. For this, however, it will be necessary to determine R2 for each note, and then select resistors for them - I'm putting this off for later.

Ultimately, a given note should play only when the decoder outputs a value of 1 for that note. For this to work, the circuit needs to be able to turn ON a given resistor set to the clock. You can easily do this with transistors - before the potentiometer I plug such an NPN transistor so that the emitter leg is at the potentiometer leg and the collector leg is connected to the clock. Now when I power it up, the speaker doesn't emit any sound which is logical, but only when I provide current to the transistor base will such a sound emerge.

I decided to do a small experiment - I connect the base to one of the EEPROM outputs, for example the most significant bit. This way I can simulate what will happen if a given resistor set is enabled by the decoder. So I run the entire circuit with counters (after previously connecting the clock in monostable mode to "click through" the successive addresses) and observe (or rather hear) the effects.

<div align="center">
  <img src="../../photos/week1/week1_generatorTest1.jpg" width="60%" alt="Sound generator - experiment 1">
</div>

Everything works as it should! When the LED lights up on the most significant bit, the speaker produces a sound. Since I have more time, I'll add a second potentiometer and connect it similarly to the previous one, but connect it to the second most significant bit. At the same time, I'll tune both sounds so that on one potentiometer is C, and on the other is D.

<div align="center">
  <img src="../../photos/week1/week1_generatorTest2.jpg" width="60%" alt="Sound generator - experiment 2">
</div>

It also works as it should! However, I noticed one thing - when two LEDs (which correspond to providing a signal to the transistor bases) light up at the same time, the sound coming from the speaker is not a simultaneously combined C and D sound (interval), but a completely different single sound. This is logical, because when two resistor sets are turned on, the total resistance is different - and so the sound is different. If I wanted to create intervals or chords, I would probably need to assemble more generators. Of course I don't lack NE555 circuits, but it will additionally complicate the circuit, and also the recording of notes in EEPROM memory and reading those values. For now, one sound at a time is more than enough.

Being already with the EEPROM, I thought about how to deal with note length. The most "crude" way would be to write a given note X times in a row, so the audible sound would last longer. However, there is a very high probability that the pauses between "changing" the sound (i.e. changing the address through the counters) will be long enough that they will be noticeable. I thought of another solution - use a decrementing counter. The recording in memory would be different, because instead of notes in order it would be alternating - note length, note, note length, note, etc. It's very easy to distinguish which value is which - assuming the above method, at even addresses are lengths and at odd addresses - notes, and it happens that this symbolizes the least significant bit in the counter. If we read a length, we put the value from EEPROM memory into a decrementing counter and read the note (by sending a signal to the address counters). Then we direct the clock signal to the decrementing counter, which slowly counts down to 0000 (at the same time the signal went to the decoder and the speaker plays). When it gets to the end, we direct the clock signal back to the address counters, we read the note length into the decrementing counter again and we start all over again.

This is just an idea for now that I need to consult, but it seems to me that it makes sense and is worth trying. When I'm sure, I'll model such a circuit in Multisim.

This week has been really productive and I'm proud of myself.

## TODO (Week 1)

- Resolution of hazard in Multisim, then purchase of appropriate logic circuits for the decoder and its assembly
- Purchase of DIP28 socket for the programmer and then soldering it to PCB
- Purchase of larger capacitors
- Consultation on the note length idea, modeling in Multisim, purchase of counter and appropriate circuits