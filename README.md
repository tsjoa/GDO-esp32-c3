# ESP32-C3 Chamberlain Garage Door Interface

## Overview
This project interfaces an ESP32-C3 Super Mini with a Chamberlain garage door opener using discrete transistors (A3400 and 2N7000) to safely read and control the signal line.

It allows:
- Reading the door signal state
- Triggering the door (simulate button press)
- Electrical isolation and level shifting

---

## Hardware Components

- ESP32-C3 Super Mini
- A3400 P-channel MOSFET
- 2N7000 N-channel MOSFET
- Resistors:
  - R8: 1kΩ
  - R9: 10kΩ
  - R10: 10kΩ
  - R11: 10kΩ
- Breadboard / PCB
- Chamberlain garage opener (signal + ground)

---

## Pin Connections

### Chamberlain
- **White (GND)** → ESP32 GND
- **Green (Signal)** → Interface circuit input

### ESP32-C3
- GPIO (output control) → Drives 2N7000 gate
- GPIO (input read) → Reads signal via A3400 path
- 3.3V → Pull-ups
- GND → Common ground

---

## Circuit Description

### Signal Reading (A3400)
- The A3400 is used to safely sense the Chamberlain signal line.
- R9 (10kΩ) pulls the gate to ground.
- The signal line drives the MOSFET, allowing detection without loading the circuit.

### Signal Driving (2N7000)
- The 2N7000 pulls the signal line low when activated.
- R10 (10kΩ) ensures the MOSFET stays off by default.
- R11 (10kΩ) limits current into the gate.

### Pull-up and Protection
- R8 (1kΩ) provides a controlled connection to the ESP32 input.
- Ensures stable logic levels and protects GPIO.

---

## Operation

### Idle State
- Signal line floats/high (via Chamberlain internal pull-up).
- ESP32 reads the state via A3400.
- 2N7000 is OFF.

### Trigger Door
1. ESP32 sets GPIO HIGH
2. 2N7000 turns ON
3. Signal line pulled to GND
4. Chamberlain interprets as button press

### Read State
- ESP32 reads GPIO connected through A3400
- Logic level corresponds to door state

---

## ESPHome YAML (Example)

```yaml
switch:
  - platform: gpio
    pin: GPIOX
    id: door_trigger
    inverted: false
    on_turn_on:
      - delay: 500ms
      - switch.turn_off: door_trigger

binary_sensor:
  - platform: gpio
    pin: GPIOY
    name: "Garage Door State"
```

Replace `GPIOX` and `GPIOY` with your actual pins.

---

## Notes

- Uses 3.3V logic only (safe for ESP32-C3)
- No direct connection between ESP32 GPIO and Chamberlain signal
- MOSFETs provide isolation and protect both devices
- Works reliably without requiring optocouplers

---

## Images

Refer to:
- Schematic diagram
- Breadboard / PCB layout

---

## Summary

This design provides a simple, reliable, and safe interface between an ESP32-C3 and a Chamberlain garage door opener using minimal components and proper signal isolation.

