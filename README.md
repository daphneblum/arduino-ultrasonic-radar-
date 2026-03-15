# Ultrasonic Radar Scanner

This project implements a rotating ultrasonic distance scanner using an Arduino, an HC-SR04 ultrasonic sensor, and an SG90 servo motor. Distance readings are transmitted over serial and visualized in a Processing application as a radar-style display.

This project was built while learning Arduino hardware integration and was adapted from an online tutorial. The system was assembled, wired, debugged, and modified to run on my hardware setup.

## Original Tutorial

This project was based on the tutorial:
[https://www.youtube.com/watch?v=SvLObGL-5ZY]

I adapted the code and hardware setup while learning how to integrate sensors, servo motors, and serial communication with Arduino.

---

## Requirements

### Hardware
- Arduino Uno (or compatible board)
- HC-SR04 ultrasonic sensor
- SG90 servo motor
- Jumper wires
- USB cable

### Software
- [Arduino IDE](https://www.arduino.cc/en/software)
- Servo library (included with Arduino IDE)
- [Processing IDE](https://processing.org/download)

---

## Wiring

| Component | Pin | Arduino Pin |
|-----------|-----|-------------|
| HC-SR04 | Trig | 10 |
| HC-SR04 | Echo | 11 |
| HC-SR04 | VCC | 5V |
| HC-SR04 | GND | GND |
| SG90 Servo | Signal | 12 |
| SG90 Servo | VCC | 5V |
| SG90 Servo | GND | GND |

---

## How It Works

### Arduino (`radar.ino`)

The servo sweeps continuously from **15° to 165°** and back, pausing 30ms at each degree. At every step, the HC-SR04 sensor fires a 10µs ultrasonic pulse and measures the echo return time. Distance is calculated as:

```
distance (cm) = duration (µs) × 0.034 / 2
```

Each reading is sent over serial at **9600 baud** in the format:

```
angle,distance.
```

For example: `90,25.` means the sensor is pointing at 90° and detected an object 25 cm away. The period (`.`) acts as a message terminator.

### Processing (`radar_viusalization.pde`)

The Processing sketch reads serial data, parses the angle and distance values, and renders a real-time radar display (1200×700 px) with:

- **Green arc lines** marking distances at ~10 cm, 20 cm, 30 cm, and 40 cm
- **Angle gridlines** at 30°, 60°, 90°, 120°, and 150°
- **A sweeping green line** tracking the current servo angle
- **A red dot/line** marking detected objects within range
- **A status bar** showing the current angle, distance, and whether an object is "In Range" or "Out of Range"

Detection is capped at **40 cm** — objects beyond that are labeled "Out of Range."

---

## Setup & Usage

1. Wire the components according to the wiring table above.
2. Upload `radar.ino` to your Arduino using the Arduino IDE.
3. Open `radar_viusalization.pde` in the Processing IDE.
4. **Update the serial port index** in `radar_viusalization.pde` line 19:
   ```java
   myPort = new Serial(this, Serial.list()[3], 9600);
   ```
   Change `[3]` to match the index of your Arduino's port. You can check available ports by printing `Serial.list()` in Processing, or by checking the Arduino IDE's Tools > Port menu.
5. If needed, update the window size on line 17 to match your screen resolution:
   ```java
   size(1200, 700);
   ```
6. Run the Processing sketch. The radar display will appear and update in real time as the servo sweeps.

---

## Files

| File | Description |
|------|-------------|
| `radar.ino` | Arduino sketch — controls servo, reads HC-SR04, sends serial data |
| `radar_viusalization.pde` | Processing sketch — parses serial data and renders radar display |
