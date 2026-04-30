# Week 7. (14-21.04.2026)

This week I basically had "cosmetic" things left to do, so:

**Soldering the jack socket**

I went shopping at the electronics store again for a jack socket. Unfortunately only mono ones were available, but oh well, I'll take what's there.

I don't have any documentation for it, there are just 3 pins which I don't quite know how they work. On the other hand, it's just a jack socket - one pin has to be +, another -, but what does this 3rd one do? Could it be a stereo socket after all?

I used the multimeter that had arrived and that I ordered when measuring the capacitor *ahem* "100nF" last week. It also has a continuity tester so it's ideal.

I tested the pins: the one that connected to the metal housing of the socket is ground, the middle one is +, and the last one is to check if a wire is currently inserted into the socket - it only connected when there was no wire in the socket. I read about it online and indeed such sockets exist. I'm happy that I figured it out on my own - without the help of the Internet. It's a small achievement, but the fact that I can use a meter boosts my confidence.

All that was left was to solder the wires to the socket and plug them into the circuit. Using the visit to the electronics store, I also bought two slide switches, I'll use one to switch between outputting the signal to the socket or the speaker (which I'm keeping in the circuit). This is nice in that the music box can always be used whether or not you have external speakers on hand.

<div align="center">
  <img src="../../photos/week7/week7_jack.jpg" width="60%" alt="Jack socket">
</div>

It works as needed, great.

**Finding the reason for the non-playing circuit**

I thought about this for a very long time, I really didn't know what could be wrong. Until one day, on a beautiful, warm evening, for no particular reason, I looked at how the EEPROM was connected on the board - two address lines that I wasn't using were missing connections to ground…

I hit my head, added the ground connections and sat testing for an hour straight if this was indeed what was causing the error. Of course it was - I probably lost these two connections somewhere during the rearrangement in week 4. This was key - I needed the EEPROM to work in the range 00000XXXXXXXX, where X is the addresses from the counters. When these addresses weren't connected to ground, they didn't have a fixed state - they could be either 1 or 0! When the circuit didn't play, these pins probably set themselves to 1, which meant the circuit worked but in a completely different range. What's more - with the "zeroed" EEPROM the bits at these addresses had value 1, and I observed this exactly when I briefly connected an LED to the EEPROM outputs. Thinking about the LED, I remembered that the circuit sometimes started working after connecting the LED - probably because it changed the state of these floating pins to 0 and that's why it worked! Similarly, when pressing the wires - the circuit sometimes worked not because I improved the wire seating, but probably because I touched an exposed wire, which added me as ground to the circuit, and probably reset it.

Well, a lesson for the future to check everything immediately and multiple times…

**Finding a solution for the pause in sound during song playback**

I also thought about this for a long time, I even considered installing D flip-flops on each signal to extend it for the duration of writing the next note's length to the down counter. But then I also hit a wall - I didn't want to buy flip-flops (I've been to this store so many times the sales guy recognizes me by voice on the phone), but I was also running out of space (if I wanted to build something like that on AND and NOT, or even on transistors). The problem was that I arranged the clock signal controller that way myself - taking it apart and rebuilding it was out of the question - too much fiddling around + no guarantee I'd fit. So I took a look at the Karnaugh map I made for the controller and noticed one thing:

<div align="center">
  <img src="../../photos/week7/week7_fix.png" width="30%" alt="MEM Repair">
</div>

If I put a 1 in the marked place, it might work. Then, when the clock is in LOW state, the counter will count to 0 and we'll be on the note, let's send the signal to memory right away. Then the read might last longer and we'll avoid those gaps between notes. Worth trying as a last resort. If it doesn't work, I'll have to install D flip-flops according to my original plan.

So I got to work, it's just adding one condition and making an OR. I didn't have transistors on hand, so I used circuits with AND and NOT gates. So I connected the negated CLK signal and ZERO to an AND gate, and its output I connected to the input of another one along with the LSB signal - this way I got a three-input AND gate. Now I just need to connect the output from this gate to an OR gate and connect the output to the clock pins of the counter. And here a problem appeared - I don't have OR gates, and I don't even have one free transistor. And here I used the knowledge I gained in "Mathematical Logic", specifically de Morgan's law:

Thanks to this, using AND and NOT gates I can make an OR. So signal A will be the signal from the existing clock controller to the address counters, and signal B - the signal from the gate I just built.

After assembly and pressing play, a beautiful, rhythmic sound of the song without pauses reaches my ears. Only now do I hear what I wanted to hear from the beginning. I also breathed a sigh of relief that the problem was solved cleverly without complications.

**Adding more songs to EEPROM memory**

Unfortunately I ran out of time and only managed to add two less impressive songs.

**Additions**

To the power supply circuit I added a slide switch and a power indicator LED. This way you can remove and insert EEPROMs without having to disconnect the circuit from the USB socket (after power off the LED quickly goes out, and the voltage is about 1.5V and drops. This is of course caused by the capacitor which discharges after power is cut off).

I also added a regular 10k linear potentiometer instead of a precision potentiometer at the main clock to more easily adjust the song speed.

So it was time for a project demonstration to the instructor - with great pride I presented all the circuit's functions and the solutions I used to perfect it to the fullest. I was ready to leave the project in the lab and officially finish it, but I promised to add a few more songs to the EEPROM during class. Then the instructor and I came up with an idea of how to better organize the songs in memory.

Namely, the songs have a limit imposed from above by the counters - they must occupy a maximum of 256 addresses (8-bit address counter), i.e. 128 notes. Looking at the fact that the EEPROM memory I'm using has 13 address lines (i.e. 13 bits), in this circuit I'm wasting over 96% of memory! However, you could allocate this memory - I'd upload several songs to one EEPROM in such a way that by changing the values of the five most significant bits I could switch songs (which have 256 addresses each). So for example song 1 will have the 5 most significant bits = 00000, song 2 will have = 00001, song 3 - 00010, etc. To quickly solve the problem, I could even manually connect wires for these address lines to VCC or ground using Dupont jumpers (flexible wires with thin ends).

I agreed to this setup, so I quickly modified the Python program for programming the EEPROM implementing this functionality and indeed the circuit worked! But at that exact moment my perfectionist nature woke up - I remembered that during my last visit to the electronics store I had also bought a DIP switch socket, which had exactly 5 outputs - perfect for my EEPROM. This way I'd avoid ugly hanging wires and everything would be done by switching. With this solution, I can upload 2^5 = 32 songs to my circuit, and additionally there's no need to remove and insert the EEPROM into the circuit. So I postponed submitting the project for another week to finish this functionality, add more songs, and complete this report.

This week that's all, there's not much left to do and the project will be finished. On the other hand, it's a shame - I'm having so much fun working on it that I'd gladly add double the functionality. But unfortunately engineering reports won't write themselves :(

## TODO (Week 7)

- Adding DIP switch for song selection
- Adding more songs
- Completing the report