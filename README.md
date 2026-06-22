# APPS Sensor — Dual-Channel Plausibility Check

Accelerator Pedal Position Sensor (APPS) firmware for STM32, implementing a dual-channel plausibility check as required in Formula Student vehicle safety systems.

Simulated on [Wokwi](https://wokwi.com/projects/377013680233224193)

## Overview

APPS sensors in Formula Student cars must be redundant — two independent sensors read the pedal position simultaneously. If their readings diverge significantly, it indicates a sensor fault or wiring issue and the system must respond safely.

This firmware reads both ADC channels continuously and compares their values. If the two signals differ by more than **10%** for longer than **3 seconds**, the system:

1. Prints a `WARNING: OUT OF BOUNDS` message over UART
2. Pulls the fault output pin (PA6) LOW to signal a fault condition
3. Enters a locked fault state (infinite loop) — preventing further operation

## How It Works

```
ADC CH0 ──┐
           ├──► diff() check ──► >10% for 3s? ──► FAULT (PA6 LOW, halt)
ADC CH1 ──┘
```

The plausibility function computes divergence as a percentage of the average:

```c
int diff(int a, int b) {
    int diff = abs(a - b);
    int avg  = (a + b) / 2;
    if (avg == 0) return diff != 0;
    return (diff * 10) > avg;  // true if divergence > 10%
}
```

## Hardware

| Component | Detail |
|---|---|
| MCU | STM32 (STM32C0 series) |
| ADC | ADC1, 12-bit resolution |
| Sensor inputs | PA0 (CH0), PA1 (CH1) — two potentiometers |
| Fault output | PA6 — pulled LOW on fault |
| Simulation | [Wokwi](https://wokwi.com/projects/377013680233224193) |

## Files

| File | Description |
|---|---|
| `main.c` | Main firmware — ADC init, plausibility loop, fault handling |
| `main.h` | HAL includes and pin definitions |
| `diagram.json` | Wokwi circuit diagram |
| `wokwi-project.txt` | Wokwi project link |
| `Setup.png` | Circuit setup screenshot |


## Author

**Luka Sičić** — [@Luka-Sicic](https://github.com/Luka-Sicic)  
