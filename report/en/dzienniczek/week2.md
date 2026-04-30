# Week 2. (10-17.03.2026)

This week was significantly less productive than the previous one, as I tried to solve the problems from the previous week. So:

**Hazard Resolution in Multisim**

Unfortunately, I couldn't solve the problem yet, mainly due to a short consultation. I still tried to come up with something (remove circuits from subcircuits, add flip-flops on the outputs), but to no avail. I'm putting the problem off to subsequent weeks, maybe I can have a longer talk about the issue in class.

**Purchase of appropriate logic circuits for the decoder, purchase of DIP28 socket for the programmer and purchase of larger capacitors**

I managed to find a stationary electronics store in Krakow and decided to visit it. I picked up the NOT gates needed for the decoder, a DIP28 socket and capacitors of 1μF and 10μF. With these I'm able to do a bit more and solve some of the problems from last week.

With the capacitors, I'm able to adjust the main clock to have a much lower frequency. So I used R1=1k and C=1μF, and instead of R2 I installed a precision potentiometer 100k with a 22k resistor - this way I can adjust the frequency to my liking. Ultimately, however, instead of a precision potentiometer I would like to install a regular potentiometer there, because you need to "turn" a lot before the frequency change is visible to the naked eye.

With the DIP28 socket in hand, I'm now able to build the final EEPROM programmer, so…

**...and then soldering it (the programmer) to PCB**

Since I wanted to be comfortable inserting and removing memory from the programmer and have the most stable connections possible (and at the same time wanted to practice soldering) I decided to make long traces. This way you don't have to squeeze between wires to insert/remove the memory, and the connecting wires to Arduino are next to each other and not subject to as much force (because of this they sit stable in the sockets). I also added a few LEDs - one to check power and three to the WE, OE and CE control pins - to more easily check the programmer's operation. The whole thing took me two nights, and the amount of solder used in the desoldering pump and "ugly" words when for the nth time I had to fix a trace because it short-circuited with a neighboring one - priceless. On the other hand, you can see that tracing from trace to trace went better and better for me and the soldering was cleaner. Nevertheless, I'm glad I finally had the opportunity to play with a soldering iron in such an ambitious way - this is an experience that can't be bought anywhere and will definitely come in handy in the future. The programmer itself works and the problem with erroneous reads and writes disappeared.

<div align="center">
  <img src="../../photos/week2/week2_EEPROM1.jpg" width="60%" alt="Programmer 1">
</div>

<div align="center">
  <img src="../../photos/week2/week2_EEPROM2.jpg" width="60%" alt="Programmer 2">
</div>


**Consultation on the note length idea, modeling in Multisim, purchase of counter and appropriate circuits**

I presented my idea and got the instructor's approval, so I got to work. First, I modeled my circuit in Multisim to make sure it does what it's supposed to do, and what else needs to be purchased. For complete success, however, I needed a memory that I could use normally like a regular EEPROM. In the program I found 3 solutions:

- 1:1 EEPROM memory circuits - yes they exist, but they serve only schematic purposes and have no functionality. Doesn't work.
- Word generator - it does have the ability to store information, but reading is handled by the generator itself - you can't access a given address through e.g. counters. Also doesn't work.
- 2K8RAM circuit - it is a full-fledged memory, but to write data to it you need to use the word generator before testing the circuit. For longer sequences this would be problematic.

I decided to go the "easy route" and use 4 buttons, which play the role of information read from memory. After setting the appropriate value on these buttons, the value is read into the down counter, which then counts down to 0. When this happens, the clock signal must go to the address counter circuit, and then after two signals return to the time counter. For this to work, I need to model a controller that allows me to direct the signal to the appropriate counters depending on the state of the time counter and which value we're reading from EEPROM (note or time). For this I'll use the knowledge I gained in "Digital Technology" and perform minimization using Karnaugh maps:

<div align="center">
  <img src="../../photos/week2/week2_gray.png" width="30%" alt="Gray code">
</div>

First, however, I describe the operation of my controller using Gray code. Designations: CLK - clock signal, ZERO - BORROW signal from the time counter (i.e. whether it reached 0000, NOTE - it is active LOW, so it will have a value of 0 when it reaches 0000), LSB - Least Significant Bit in the address counters (i.e. whether it reads time or note), MEM/TIM - whether the clock signal goes to the address/time counters.

In summary - the clock signal goes to the time counter only when this counter is still counting (ZERO has value 1, i.e. hasn't reached 0000) and at the same time LSB is 1 (i.e. a note is playing). However, when the counter reaches 0, regardless of LSB value - the signal goes to the address counter (because anyway it has to go through bit 1 -> 0 and 0 -> 1). The same happens when LSB has value 0 and ZERO has value 1 (i.e. time counter has a value in it and address counter is on note time), but then when CLK is 0. In my opinion this might work, so let's proceed with the minimization:

<div align="center" style="display: flex; justify-content: center; gap: 20px;">
  <img src="../../photos/week2/week2_MEM.png" width="30%" alt="Minimized MEM">
  <img src="../../photos/week2/week2_TIM.png" width="30%" alt="Minimized TIM">
</div>


I got two functions, thanks to which I can realize the circuit. I'll definitely need 3 NOT gates, one OR and several ANDs. So I proceed to build the controller in Multisim:

<div align="center">
  <img src="../../photos/week2/week2_multi1.png" width="60%" alt="Multisim - 1">
</div>

Let's model the rest of the circuit - counters/buttons etc. so you can test how the controller works:  

<div align="center">
  <img src="../../photos/week2/week2_multi2.png" width="60%" alt="Multisim - 2">
</div>

I marked current and ground in red and black respectively. Blue - clock signal, purple - counter outputs (connected to test points and controller) and green - signal from buttons. After testing I find that the circuit works as intended.

Using a visit to the electronics store I purchased a 4-bit down counter, but they didn't have the logic circuits I was interested in (or at what price I would be interested in). I was already thinking of giving up for now and buying the gates over the internet at a later time, but I remembered two things:

- In the project at least one of its parts must be built low-level (half jokingly half seriously, actually I think my whole project is low-level 😉)
- I have a set of transistors that I was supposed to use eventually for the circuit controlling the sound frequency

So why not make gates out of transistors? After all, my circuit is relatively small compared to the number of gates, and it's always extra experience! So I get to work, or actually to looking for schematics so I can use them to build my gates (I'm writing this afterwards, I tried to come up with something on my own and burned two transistors). I found a very helpful video:

<https://www.youtube.com/watch?v=I6ZyZPJ0MXM> (Link active as of 4.04.2026)

It shows how to build such gates using transistors. Using this knowledge I finally got to work.

I didn't manage to finish the circuit in time, so I'll have to put it off to next week.

This week was enough. Less productive than the previous one, but I managed to get a decent amount done.

## TODO (Week 2)

- Resolution of hazard in Multisim, then purchase of appropriate logic circuits for the decoder (AND) and its assembly
- Completion of clock controller circuit on transistors and then assembly of the entire circuit