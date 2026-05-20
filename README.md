# Connors RSI Alert — MQL4 Script

A MetaTrader 4 script that computes the **Connors RSI (CRSI)** — a three-component composite momentum oscillator combining a short-period RSI, a streak-length RSI, and a percentage rank of the current bar's relative price change — and fires overbought/oversold alerts when the composite value crosses configurable threshold levels.

---

## Overview

Connors RSI was developed by Larry Connors as a higher-resolution momentum indicator that addresses RSI's single-component limitations by fusing three independent momentum measurements into one normalized 0–100 composite score. Component one is a short-period RSI on close prices. Component two is an RSI applied to the current consecutive up/down streak count, capturing directional persistence. Component three is the absolute percentage change of the current bar relative to its prior close, measuring relative rate-of-change. The three components are averaged with equal weighting. This script implements all three components natively in MQL4 and fires alerts when the composite CRSI reaches extreme overbought or oversold territory as defined by the configurable threshold inputs.

---

## Features

- **Three-component CRSI construction:**
  - **RSI₁:** `iRSI()` on close prices over `RSI_Period` bars
  - **StreakRSI:** `iCustom("RSI", StreakRSI_Period, streak)` applied to the directional streak value
  - **RelChange:** `|close − prevClose| / prevClose × 100` percentage rate-of-change
- **Streak calculator** — `CalculateStreak()` walks up to 100 bars backward, incrementing streak for consecutive higher closes and decrementing for consecutive lower closes, breaking on a flat bar
- **Sufficient-data guard** — `MathMax(rsiPeriod, MathMax(streakRsiPeriod, relChangePeriod))` ensures minimum bar count before calculation proceeds
- **Independent threshold detection** — overbought and oversold checks evaluated separately to allow asymmetric threshold configuration
- **Three notification channels:** sound alert, email, and mobile push
- **Lightweight loop** — polls once per minute (`Sleep(60000)`)
- Logs CRSI composite value and alert type to the MT4 **Experts** tab on every trigger

---

## How It Works

1. Every minute, `CalculateConnorsRSI()` computes all three components and returns their unweighted average: `CRSI = (RSI₁ + StreakRSI + RelChange) / 3.0`
2. `CalculateStreak()` counts consecutive same-direction close changes from bar 1 backward, returning a signed integer streak value
3. `StreakRSI` is computed via `iCustom(..., "RSI", streakRsiPeriod, streak, 0)` passing the raw streak as a price input to the built-in RSI indicator
4. Two independent threshold comparisons fire alerts:
   - `crsi >= OverboughtLevel` → **Overbought Condition Detected**
   - `crsi <= OversoldLevel` → **Oversold Condition Detected**

---

## Input Parameters

| Parameter            | Type            | Default     | Description                                                   |
|----------------------|-----------------|-------------|---------------------------------------------------------------|
| `TradeSymbol`        | string          | `EURUSD`    | Symbol for analysis                                           |
| `Timeframe`          | ENUM_TIMEFRAMES | `PERIOD_H1` | Timeframe for analysis                                        |
| `RSI_Period`         | int             | `3`         | Period for the primary RSI component                          |
| `StreakRSI_Period`   | int             | `2`         | RSI period applied to the streak value                        |
| `RelChangePeriod`    | int             | `100`       | Lookback period for relative change percentile ranking        |
| `OverboughtLevel`    | double          | `90.0`      | CRSI value at or above which an overbought alert is triggered |
| `OversoldLevel`      | double          | `10.0`      | CRSI value at or below which an oversold alert is triggered   |
| `EnableAlerts`       | bool            | `true`      | Fire an on-screen/sound alert                                 |
| `EnableEmail`        | bool            | `false`     | Send an email notification                                    |
| `EnablePush`         | bool            | `false`     | Send a mobile push notification                               |

---

## Alert Message Format

```
Overbought Condition Detected on EURUSD (Timeframe: PERIOD_H1)
CRSI Value: 92.15
```

---

## Installation

1. Copy `Connor_RSI_001.mq4` to `MQL4/Scripts/` in your MT4 data folder
2. Compile in MetaEditor (F7)
3. Drag onto any chart from Navigator → Scripts
4. Configure inputs and click **OK**

---

## Requirements

- MetaTrader 4 (`#property strict` compatible build)
- MQL4 compiler (MetaEditor)

---

## License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
