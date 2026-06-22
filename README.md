// Written using Claude Sonnet 4.6, Low Effort //

# ISS LED Tracker

An ESP32 project that lights up an LED whenever the International Space Station passes within 400km of a fixed location. Also displays a running distance counter and current coordinates via Serial.

## What It Does

- Fetches the ISS's current latitude and longitude every 30 seconds from the [Where the ISS At?](https://wheretheiss.at/w/developer) API
- Calculates the great-circle distance between the ISS and a fixed reference point using the Haversine formula
- Turns on an LED when the ISS is within 400km of that point
- Prints current ISS coordinates and distance

## Hardware Required

- ESP32 development board
- LED connected to pin 2 (with appropriate resistor)
- WiFi connection

## Software Dependencies

- [ArduinoJson](https://arduinojson.org/) v7
- [PlatformIO](https://platformio.org/) with the ESP32 Arduino framework

## Setup

1. Clone this repository:
   ```bash
   git clone https://github.com/aaronneiman/ISS_LED.git
   ```

2. Create a file called `secrets.h` in the `include/` folder with your WiFi credentials and location:
   ```cpp
   #define MY_WIFI_SSID "your_wifi_name"
   #define MY_WIFI_PASSWORD "your_wifi_password"
   #define MY_LAT 00.000000   // your latitude
   #define MY_LONG 00.000000  // your longitude
   ```
   This file is excluded from version control via `.gitignore` to protect your credentials and location.

3. Open the project in VS Code with PlatformIO installed.

4. Build and upload to your ESP32.

## How It Works

The core of the distance calculation is the **Haversine formula**, which computes the shortest distance between two points on the surface of a sphere (in this case, Earth). This accounts for the curvature of the Earth, unlike a simple straight-line distance calculation.

```
a = sin²(Δlat/2) + cos(lat1) × cos(lat2) × sin²(Δlon/2)
c = 2 × atan2(√a, √(1−a))
distance = R × c
```

Where R is Earth's radius (6,371 km).

## Project Structure

```
ISS_LED/
├── src/
│   ├── main.cpp          # Entry point, setup() and loop()
│   ├── iss_tracker.cpp   # HTTP fetch, JSON parsing, LED control
│   ├── iss_tracker.h
│   ├── elapsed_time.cpp  # NTP time sync, elapsed time calculation
│   └── elapsed_time.h
├── include/
│   └── secrets.h         # WiFi credentials and location (not tracked by git)
├── platformio.ini
└── README.md
```

## API

ISS position data is provided by [Where the ISS At?](https://wheretheiss.at/w/developer), a free, no-auth-required REST API that returns current ISS coordinates updated approximately once per second.

Example response:
```json
{
  "name": "iss",
  "id": 25544,
  "latitude": 50.11,
  "longitude": 118.07,
  "altitude": 408.05,
  "velocity": 27635.97,
  "visibility": "daylight",
  "timestamp": 1364069476,
  "units": "kilometers"
}
```

## Notes

- `secrets.h` is intentionally excluded from this repository. You must create it yourself before building.
- The 400km threshold can be adjusted in `main.cpp` to make the trigger more or less sensitive.
- The elapsed time counter uses NTP to sync the ESP32's clock on startup, so a WiFi connection is required.
