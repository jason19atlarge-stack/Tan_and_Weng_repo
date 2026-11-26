## How it works

This project is a seven-segment display controller built entirely from basic logic gates (AND, OR, XOR, NOT) and three D flip-flops. It has two kinds of function: a small calculator, and a character sequencer.

### Pin usage

| Pin | Function |
|---|---|
| `ui_in[0]` – `ui_in[5]` | Calculator operands |
| `ui_in[6]` | Mode select: 0 = calculator, 1 = sequence |
| `ui_in[7]` | Sub-select (see the table below) |
| `uo_out[0]` – `uo_out[6]` | Segments a – g |
| `uo_out[7]` | Decimal point, connected directly to the clock |
| `clk` | Clock (1 Hz recommended for viewing) |
| `rst_n` | Active-low reset |

### Modes

| `ui_in[6]` | `ui_in[7]` | Function |
|---|---|---|
| 0 | 0 | **Add**: A + B + C, where A = `ui_in[1:0]`, B = `ui_in[3:2]`, C = `ui_in[5:4]` (each 0–3), result 0–9 |
| 0 | 1 | **Absolute difference**: \|X − Y\|, where X = `ui_in[2:0]`, Y = `ui_in[5:3]` (each 0–7), result 0–7 |
| 1 | 0 | **Date sequence**: shows `2 0 2 6 1 1 2 6` one character per clock |
| 1 | 1 | **HAPPY sequence**: shows `H A P P Y` one character per clock |

### Calculator modes

The calculator modes are purely combinational and respond immediately, without needing a clock.

- **Add:** three 2-bit numbers are added by a chain of full-adder gates, giving a result from 0 to 9.
- **Subtract:** two subtractors run in parallel, one computing X − Y and one computing Y − X. A comparator then selects whichever result is non-negative, so the display always shows \|X − Y\|.
- **Output selection:** the add and subtract results go through a 2-to-1 multiplexer controlled by `ui_in[7]`. The selected 4-bit value drives a gate-level BCD-to-seven-segment decoder.

### Sequence modes

A 3-bit counter made of three D flip-flops steps through the characters, advancing once per rising clock edge.

- **Date mode** counts 0 to 7 and wraps back to 0, showing 2-0-2-6-1-1-2-6.
- **HAPPY mode** counts 0 to 4 and wraps back to 0, showing H-A-P-P-Y.
- If HAPPY mode is selected while the counter is already at 4 or higher, it returns to 0 on the next clock edge, so the word always starts from H.
- While in a calculator mode, the counter is held at 0. Every sequence therefore restarts from its first character when you switch back.
- The reset input also forces the counter to 0.

### Decimal point

The decimal point is wired directly to the clock. It toggles with every clock cycle, which shows that the clock is running. It also separates repeated characters such as the two 1s in the date and the two Ps in HAPPY, since consecutive identical characters would otherwise look the same.

## How to test

1. Connect a common-cathode seven-segment display to `uo_out[7:0]` (a = bit 0 … g = bit 6, DP = bit 7).
2. Power the board and apply a slow clock, such as 1 Hz. Pulse `rst_n` low once, then release it.
3. **Add test:** set `ui_in[7:6]` = `00`. Try A = 3, B = 3, C = 3 (`ui_in[5:0]` = `111111`); the display should show 9. All zeros should show 0.
4. **Subtract test:** set `ui_in[7:6]` = `10`. Set X = 5 (`ui_in[2:0]` = `101`) and Y = 2 (`ui_in[5:3]` = `010`); the display should show 3. Swap the two values and confirm it still shows 3, then set X = Y and confirm it shows 0.
5. **Date test:** set `ui_in[7:6]` = `01`. The display should step through 2, 0, 2, 6, 1, 1, 2, 6 and then repeat.
6. **HAPPY test:** set `ui_in[7:6]` = `11`. The display should step through H, A, P, P, Y and then repeat.
7. Watch the decimal point. It should toggle with the clock in every mode.
8. Switch between modes while running. Calculator modes should update immediately, and each sequence should restart from its first character when selected.

## External hardware

- One common-cathode seven-segment LED display (the decimal point is used)
- Eight input switches or a DIP switch for `ui_in[7:0]`
- A clock source, if the Tiny Tapeout board's clock is not used (a 1 Hz clock is easiest to read)
- A reset push button or switch for `rst_n`
- A current-limiting resistor of about 330 Ω – 1 kΩ on each segment, unless the display module already has them
