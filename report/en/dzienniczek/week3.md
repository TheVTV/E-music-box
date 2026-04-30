# Week 3. (17-24.03.2026)

Actually, right after returning from class I sit down with those transistors because I'm curious if it will work:

**Completion of clock controller circuit on transistors and then assembly of the entire circuit**

I finished the circuit, but I didn't bother cutting the wires and legs nicely yet, so for now it's one big spider web:

<div align="center">
  <img src="../../photos/week3/week3_controller1.jpg" width="60%" alt="Controller - 1">
</div>

Interestingly, I managed to reduce two two-input gates into one three-input gate, that's just a matter of adding one transistor - good to know for the future.

So let's see if it works:

<div align="center">
  <img src="../../photos/week3/week3_controller2.jpg" width="60%" alt="Controller - 2">
</div>

There was some debugging (mainly because of touching resistors + I managed to mess up some connections on the gates) but the situation was handled and the circuit works almost like in Multisim! Almost, because instead of manually setting the data with buttons, I connected the EEPROM memory outputs to the counter (after uploading example data beforehand). So you could say double success! So let me clean up the "web" I made while assembling the controller (I'm a perfectionist and it hurts my eyes).

It looks much better… A bit more debugging but the circuit works like before, so I have this todo item checked off!

**Resolution of hazard in Multisim, then purchase of appropriate logic circuits for the decoder (AND) and its assembly**

During the previous class I managed to find a longer moment for consultation about the hazard. After removing the circuit from subcircuits, cleaning it up and taking a look, I managed to locate the probable problem - the octave signal from EEPROM arrives faster at the second part of the decoder. After thinking about it, it makes sense - N0-N4 still have to go through the note decoder before the octave decoder. So the octave signal needs to be slightly delayed, e.g. using a buffer. After adding it, the hazard disappears and the entire circuit runs as it should!

<div align="center">
  <img src="../../photos/week3/week3_multi1.png" width="60%" alt="Final Multisim">
</div>

I know the solution to the problem, but I won't implement it directly for now - maybe it will be OK? If needed, I'll add a buffer, which can even be replaced by multiple passages of the signal through NOT gates, which will add slight delay.

This week has been enough. Now I can work on implementing the decoder, but to do that I need to have gates in circuits (making them on transistors will be time- and space-consuming). Maybe I didn't get much done, but I did clear the path to start building the decoder!

## TODO (Week 3)

- Purchase of appropriate logic circuits for the decoder (AND) and its assembly