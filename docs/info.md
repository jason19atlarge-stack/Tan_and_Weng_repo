## How it works

This project is a digital seven-segment display controller implemented in Verilog.

The circuit uses digital logic gates to control a seven-segment display. Different input combinations select different numbers or letters to display.

The controller supports numerical output and the word "HAPPY" using seven-segment character patterns.

A clock input is used for sequential functions. The decimal point changes its state with clock activity, providing a visual indication of the clock.

The design combines combinational logic for display decoding and sequential logic for clock-controlled operations.

## How to test

1. Connect the seven-segment display to the appropriate output pins.
2. Supply power and a clock signal to the circuit.
3. Change the input switches to select different numbers and display modes.
4. Check that the seven-segment display shows the expected numbers and letters.
5. Select the HAPPY display mode and verify the character patterns.
6. Apply a slow clock, such as 1 Hz, and observe the decimal point changing state.
7. Verify that the display responds correctly when the control inputs change.

## External hardware

- Seven-segment LED display
- Input switches for selecting numbers and display modes
- Clock source, if not provided by the Tiny Tapeout board
- Suitable current-limiting resistors and display-driving circuitry, as required by the display and output specifications<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

