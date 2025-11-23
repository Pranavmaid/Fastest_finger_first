# Fastest_finger_first

A simple **Fastest Finger First** (quiz buzzer) system built using an Arduino, 5 push buttons, 5 LEDs, and a buzzer.

- **4 player buttons** (contestants)
- **1 host/reset button**
- **4 player LEDs + 1 host LED**
- **1 buzzer** to indicate the winner

When the host enables the round, the first player to press their button lights up their LED and triggers the buzzer. All other inputs are ignored until the host resets the system for the next question.

---

## Features

- Up to **4 players** (can be extended in code)
- **Host control button** to start and reset each round
- **Audible buzzer** when a player wins
- Uses Arduino’s `INPUT_PULLUP` (no external pull-up resistors needed)
- Simple logic and easy to modify

---

## Hardware Requirements

- 1 × Arduino (Uno/Nano/compatible)
- 5 × Momentary push buttons
- 5 × LEDs (4 for players, 1 for host indicator)
- 5 × Current-limiting resistors for LEDs (e.g. 220–330 Ω)
- 1 × Buzzer (active or passive; code assumes simple on/off)
- Jumper wires
- Breadboard or PCB

---

## Pin Connections

> **Note:** The code uses internal pull-ups, so each button is wired between the pin and **GND**. A pressed button reads as `LOW (0)`.

### Buttons

| Button        | Arduino Pin | Role                |
|--------------:|:-----------:|---------------------|
| `button1`     | `2`         | Player 1 input      |
| `button2`     | `3`         | Player 2 input      |
| `button3`     | `4`         | Player 3 input      |
| `button4`     | `5`         | Player 4 input      |
| `button5`     | `9`         | Host / reset button |

**Wiring (for each button):**

- One leg → Arduino digital pin (as per table)
- Other leg → **GND**

Since `pinMode(buttonX, INPUT_PULLUP);` is used, **do not** add external pull-up resistors.

---

### LEDs

| LED      | Arduino Pin | Role                   |
|---------:|:-----------:|------------------------|
| `LED1`   | `6`         | Player 1 indicator     |
| `LED2`   | `7`         | Player 2 indicator     |
| `LED3`   | `8`         | Player 3 indicator     |
| `LED4`   | `10`        | Player 4 indicator     |
| `LED5`   | `11`        | Host / round active LED (*shares pin with buzzer in this code*) |

**Wiring (for each LED):**

- LED **anode (+)** → **resistor** → Arduino pin
- LED **cathode (–)** → **GND**

> ⚠️ In the provided code, `LED5` and `buzzerPin` both use pin `11`.  
> That means the host LED and buzzer will be driven together.  
> If you want them **separate**, move `LED5` or `buzzerPin` to a different free pin and adjust the `#define`s and wiring.

---

### Buzzer

| Device     | Arduino Pin |
|-----------:|:-----------:|
| `buzzerPin`| `11`        |

**Wiring:**

- Buzzer `+` → Arduino pin `11`
- Buzzer `–` → GND  
  (Use a transistor and/or resistor if required by your buzzer specs.)

---

## How It Works (Logic)

1. **Initial state**
   - All LEDs are **OFF**.
   - `flag = 0` → system is idle; player buttons are ignored.

2. **Host starts round**
   - Host presses **button5** (pin 9).
   - `flag` is set to `1`.
   - `LED5` is turned **ON** to indicate the round is live.

3. **Players answer**
   - While `flag == 1`, the code checks `button1`–`button4`.
   - The **first** button detected as pressed:
     - Sets `flag = 0` (round locked).
     - Turns **host LED OFF**.
     - Calls `Alarm()` → buzzer beeps.
     - Turns **only that player’s LED ON**.
     - Enters `while(digitalRead(button5));` waiting for the host button.

4. **Host resets round**
   - Host presses **button5** again to exit the `while` loop.
   - On next loop iteration, LEDs are cleared and host can start another round.

---
