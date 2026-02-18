---
layout: page
title: Ultrasonic Ranging System
description: Real-time distance measurement system using Arduino and HC-SR04 sensor with I2C LCD display output.
img: assets/img/ultrasonic/thumbnail.jpg   # update with your actual image path
importance: 1
category: hardware
---

## Overview

This is a project is built on a simple breadboard. The main project compponets are a HC-SR02 ultrasonic sensor, LCD 16x2 screen, Arduino uno R3. 

---

## Hardware

| Component       | Details                              |
|----------------|--------------------------------------|
| Microcontroller | Arduino Uno                          |
| Sensor          | HC-SR04 Ultrasonic Distance Sensor   |
| Display         | 16×2 LCD with I2C backpack module    |
| Range           | 2 cm – 400 cm                        |
| Power           | 5V via USB                           |

---

## How It Works

The HC-SR04 sensor works by emitting a short 40 kHz ultrasonic pulse from its "TRIG" pin and listening for the echo on the "ECHO" pin. The time-of-flight of the pulse is used to calculate distance using the speed of sound:

```
Distance (cm) = (Echo pulse duration in µs) / 58
```

The Arduino reads this pulse duration using `pulseIn()`, performs the calculation, and sends the result to a 16×2 LCD over the **I2C** bus — which keeps wiring minimal (only two data lines, SDA and SCL, plus power).

---

## Software

The firmware was written in Arduino C++ and handles three main tasks:

**1. Distance Measurement** — A dedicated function triggers the sensor, measures echo duration, and converts it to centimeters. Readings outside the sensor's valid range (2–400 cm) are flagged as out-of-range to prevent garbage output.

**2. I2C LCD Communication** — The `LiquidCrystal_I2C` library manages the display. The I2C address was confirmed with an I2C scanner sketch before integration.

**3. Formatted Output** — The LCD shows a clean, human-readable reading updated in real time:

```
Distance:
123 cm
```

---

## Key Code Snippet

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

#define TRIG_PIN 9
#define ECHO_PIN 10

LiquidCrystal_I2C lcd(0x27, 16, 2);

long measureDistance() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH);
  return duration / 58;  // convert to cm
}

void loop() {
  long distance = measureDistance();

  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Distance:");
  lcd.setCursor(0, 1);

  if (distance >= 2 && distance <= 400) {
    lcd.print(distance);
    lcd.print(" cm");
  } else {
    lcd.print("Out of range");
  }

  delay(200);
}
```

---

## Results

The system measured distances accurately across the full rated range of the HC-SR04, with stable readings at close range (2–10 cm) and consistent performance at longer distances. The I2C interface simplified wiring significantly compared to a parallel LCD connection.

---

## Tools & Skills Used

`Arduino C++` &nbsp;·&nbsp; `HC-SR04 Ultrasonic Sensor` &nbsp;·&nbsp; `I2C Protocol` &nbsp;·&nbsp; `LiquidCrystal_I2C Library` &nbsp;·&nbsp; `Arduino IDE` &nbsp;·&nbsp; `Embedded Systems`

---

## Gallery

<!-- Replace the filenames below with your actual photos once uploaded to assets/img/ultrasonic/ -->

<div class="row">
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/ultrasonic/wiring.jpg" title="Wiring diagram" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/ultrasonic/assembled.jpg" title="Assembled circuit" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/ultrasonic/lcd_output.jpg" title="LCD showing live distance" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Left: wiring setup. Center: assembled circuit on breadboard. Right: LCD displaying a live distance reading.
</div>

---

*February 2026 — Personal Project*
