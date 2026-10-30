# ARDRA LAB Firmware Assignment — FW10779

**Project:** LED Pattern Using External Interrupt  
**Target:** Original ESP32 / ESP32 DevKit V1  
**Framework:** ESP-IDF 6.0.1  
**Language:** C  
**RGB LED:** Common-anode

## Functionality

The RGB LED starts with all channels OFF. Each valid button press advances through this repeating sequence:

1. Red ON; Green and Blue OFF
2. Green ON; Red and Blue OFF
3. Blue ON; Red and Green OFF
4. Red, Green and Blue ON
5. Repeat from step 1

The button is connected as an active-low input with an internal pull-up. A falling-edge GPIO interrupt notifies a FreeRTOS task. The task performs debounce and press-lockout timing outside the ISR.

## Hardware connections

| Component | ESP32 pin |
|---|---|
| RGB Red cathode (through 220–330 Ω resistor) | GPIO 25 |
| RGB Green cathode (through 220–330 Ω resistor) | GPIO 26 |
| RGB Blue cathode (through 220–330 Ω resistor) | GPIO 27 |
| RGB common anode | 3V3 |
| Push button terminal 1 | GPIO 33 |
| Push button terminal 2 | GND |

**Important:** This pinout follows the submitted source-code PDF. The button pin is GPIO 33 (not GPIO 18). Confirm the physical wiring matches before flashing.

For a common-anode LED, LOW turns a channel ON and HIGH turns it OFF. Use a separate current-limiting resistor for each colour channel. Verify the LED pinout before connecting power.

## Register-level LED control

LED output manipulation is performed using the original ESP32 GPIO output set/clear registers:

- `GPIO_OUT_W1TS_REG`: sets selected output bits HIGH.
- `GPIO_OUT_W1TC_REG`: clears selected output bits LOW.

The code does not use Arduino `digitalWrite()`, `digitalRead()`, or `pinMode()`.

ESP-IDF GPIO APIs are used to configure the pins and install/attach the hardware GPIO interrupt. The FreeRTOS task handles debounce and state transitions.

## Project structure

```text
FW10779_ESP32_ESP_IDF/
├── CMakeLists.txt
├── README.md
├── .gitignore
├── main/
│   ├── CMakeLists.txt
│   └── main.c
└── docs/
    └── ARDRA-LAB-Assignment-FW10779.pdf
```

The demonstration video is intentionally not included in this repository bundle. Add it only if you want it publicly accessible; consider linking to a suitably shared video instead of committing a large video file.

## Build

Open an ESP-IDF 6.0.1 terminal in this project directory:

```bash
idf.py set-target esp32
idf.py build
```

To flash (replace `COMx` with the board's port):

```bash
idf.py -p COMx flash monitor
```

## Notes

- This source targets the original ESP32 register map, not every ESP32-family chip.
- Validate the build and test on the exact board before treating the repository as a verified release.
- The code PDF supplied for the assignment is retained under `docs/`.
