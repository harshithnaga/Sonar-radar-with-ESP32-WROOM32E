# ESP32 Sonar Radar

A servo-swept HC-SR04 ultrasonic sensor paired with an ESP32 and an ILI9341 TFT display, rendering a classic green radar sweep with live object detection. Designed, wired, and simulated in [BenchTwin](https://benchtwin.com/) before building on real hardware.

## How it works

An SG90 micro servo sweeps the HC-SR04 ultrasonic sensor back and forth across a 15°–165° arc. At each angle the ESP32 pings the sensor, converts the echo time to a distance, and plots the result on the TFT as a point in polar coordinates — angle from the servo, distance from the echo. The sweep line and range rings redraw every frame so it reads like an actual radar scope, with detected objects showing up as fading yellow blips.

## Hardware

| Part | Role |
|---|---|
| ESP32-WROOM-32E DevKit | Main controller |
| HC-SR04 | Ultrasonic distance sensor |
| SG90 | Micro servo, sweeps the sensor |
| ILI9341 2.8" SPI TFT (320×240) | Radar display |
| 1kΩ + 2kΩ resistors | Voltage divider for HC-SR04's ECHO line |

## Wiring

| Component pin | ESP32 GPIO |
|---|---|
| HC-SR04 TRIG | 12 |
| HC-SR04 ECHO (through divider) | 14 |
| SG90 signal | 13 |
| TFT CS | 25 |
| TFT DC | 26 |
| TFT RESET | 27 |
| TFT SDI (MOSI) | 32 |
| TFT SCK | 33 |
| TFT SDO (MISO) | 4 |

HC-SR04 VCC and SG90 VCC run on 5V (SG90 ideally from a separate 5V supply); the TFT runs on 3.3V. All grounds are common.

Full reasoning for this pin layout — including why it avoids the ESP32's strapping pins and why the HC-SR04's ECHO line needs the divider — is in the comment header of `esp32_sonar_radar.ino`.

## Repository contents

- `esp32_sonar_radar.ino` — the Arduino sketch (sweep logic, distance sensing, radar drawing)
- `sonar-radar.btw` — the full BenchTwin project (3D bench, wiring, and code in one file)

## Getting started

**Libraries** (Arduino IDE → Library Manager):
- `TFT_eSPI` by Bodmer
- `ESP32Servo` by Kevin Harrington / Dlloyd

**TFT_eSPI setup:** this library is configured at compile time, not in the sketch. Set the following in `TFT_eSPI/User_Setup.h`:

```cpp
#define ILI9341_DRIVER
#define TFT_MOSI 32
#define TFT_MISO  4
#define TFT_SCLK 33
#define TFT_CS   25
#define TFT_DC   26
#define TFT_RST  27
#define SPI_FREQUENCY 40000000
```

Then wire up the hardware per the table above, open `esp32_sonar_radar.ino`, select your ESP32 board, and upload.

## Simulating in BenchTwin

Open `sonar-radar.btw` at [benchtwin.com](https://benchtwin.com/) (drop it onto the bench, or use Share/Import) to see the full wired circuit and run the code without any hardware.

## Tuning

- `SWEEP_STEP_DEG` — angular resolution; smaller steps look smoother but scan slower
- `MAX_RANGE_CM` — detection ceiling; HC-SR04 gets unreliable past ~300–400cm
- `STEP_DELAY_MS` — time given for the servo to settle before each ping
