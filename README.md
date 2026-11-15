# Smart Security Gate (Team B5)

A smart parking access system built for the Advanced Embedded Systems Lab at Hochschule Hamm-Lippstadt. A driver taps an RFID card, and if it is approved and a vehicle is present, the gate opens, the parking count updates, and everything shows on a live web dashboard.

## How it works

- An **ESP32** runs the gate. It reads the RFID card, checks it against an allowed list, moves the servo gate, drives the LEDs and buzzer, shows messages on an LCD and keeps count of free parking spaces (7 in total).
- A **Raspberry Pi** runs a Flask web dashboard and an MQTT broker.
- The ESP32 and the Pi talk over Wi-Fi using MQTT.

Flow: idle and locked, card scanned, ID checked, wait up to 10 s for the ultrasonic sensor to see a vehicle, open, close once the vehicle has passed. A denied card gives a red LED, two beeps and "Access Denied" on the LCD. At zero free spaces the gate stays shut. If power is lost the servo stays closed (fail safe).

## Main components

- ESP32 DevKitC (main controller)
- RC522 RFID reader (3.3 V only)
- HC-SR04 ultrasonic sensor (vehicle detection)
- SG90 servo (gate arm)
- LEDs and buzzer (feedback)
- 16x2 LCD (1602A, 4-bit parallel, no I2C backpack)
- Push button
- Raspberry Pi (dashboard and MQTT broker)

## My contribution

This was a group project for a course that required four team members with assigned roles. The role split on paper does not capture how the work actually went, so here is what I did:

- **Hardware:** wired the whole prototype (RC522, ultrasonic sensor, servo, LEDs, buzzer, LCD), planned the pin assignments and sorted out power.
- **Firmware:** worked on the ESP32 code that runs the gate.
- **Documentation:** wrote the implementation section of the report, including the wiring and pin tables.
- **Dashboard:** helped with the Flask dashboard.

The Wi-Fi and MQTT broker link between the ESP32 and the Pi was mainly set up by teammates.

### Things I ran into

- **Boot pins:** code would not upload with the LCD on GPIO 12. Some ESP32 pins are read at power-up to decide how the chip boots (GPIO 0, 2 and 12). I moved the LCD lines and put the button on GPIO 12.
- **3.3 V vs 5 V:** the RC522 only runs from 3.3 V, everything else from 5 V, with one shared ground. The reader would not work with an Arduino Uno, so I moved the build to the ESP32.
- **Hotspot networking:** an iPhone hotspot blocks direct device-to-device traffic, so all messages go through the MQTT broker on the Pi.

## Pin map (ESP32)

| Part | GPIO |
|---|---|
| RC522 | 5 (SDA), 18 (SCK), 23 (MOSI), 19 (MISO), 27 (RST) |
| Servo | 14 |
| Green / red LED | 15 / 25 |
| Buzzer | 4 |
| Ultrasonic | 32 (trigger), 33 (echo) |
| LCD | 21, 22, 16, 17, 26, 13 |
| Button | 12 |

## Repository structure

- `esp32/` the Arduino code that runs on the ESP32
- `raspberry-pi/` the Flask dashboard (`app.py` and `templates/index.html`)
- `docs/` the project report (PDF)

## Setup notes

- Open `esp32/smart_gate.ino` and fill in your Wi-Fi name, password and the Pi's IP address before uploading.
- Libraries: MFRC522, ESP32Servo, LiquidCrystal, PubSubClient.
- The Pi needs Flask, paho-mqtt and an MQTT broker. Run `python app.py` and open port 5000 on the Pi's IP.

## Team

Muji (coordination and documentation), Victor (hardware integration), Jaleel (embedded software), Umer (communication and testing).
