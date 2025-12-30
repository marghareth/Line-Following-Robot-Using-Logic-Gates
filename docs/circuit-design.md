# Circuit Design Documentation

This document provides detailed information about the circuit design, component connections, and implementation of the line-following robot.

---

## Table of Contents
1. [Overview](#overview)
2. [System Architecture](#system-architecture)
3. [Component Specifications](#component-specifications)
4. [Logic Circuit Design](#logic-circuit-design)
5. [Motor Driver Configuration](#motor-driver-configuration)
6. [Power Distribution](#power-distribution)
7. [Wiring Diagrams](#wiring-diagrams)
8. [Pin Connections](#pin-connections)

---

## Overview

The line-following robot uses a **purely hardware-based control system** implemented with digital logic gates. No microcontroller or programming is involved - all decision-making is handled by Boolean logic circuits.

### Design Philosophy
- **Minimalist approach:** Use the minimum number of gates required
- **K-map optimization:** Simplify Boolean expressions to reduce complexity
- **Modular design:** Separate sensor, logic, and motor control sections
- **Testable components:** Each section can be tested independently

---

## System Architecture

```
┌─────────────┐
│  IR Sensors │ (3 sensors)
│  (I1,I2,I3) │
└──────┬──────┘
       │
       ↓
┌─────────────┐
│  Inverters  │ (NOT gates - IC 7404)
│   (I1',I2',I3')  │
└──────┬──────┘
       │
       ↓
┌─────────────┐
│ Logic Gates │ (AND/OR - IC 7408, 7432)
│  Processing │
└──────┬──────┘
       │
       ↓ (LM1, LM2, RM1, RM2)
┌─────────────┐
│  L298N      │
│ Motor Driver│
└──────┬──────┘
       │
       ↓
┌─────────────┐
│  DC Motors  │ (Left & Right)
└─────────────┘
```

---

## Component Specifications

### IC 7404 - Hex Inverter (NOT Gate)
- **Function:** Inverts input signals (0→1, 1→0)
- **Package:** 14-pin DIP
- **Supply Voltage:** 5V (Pin 14: VCC, Pin 7: GND)
- **Gates per IC:** 6 NOT gates
- **Usage in project:** Invert sensor outputs

**Pin Configuration:**
```
    7404
   ┌──┴──┐
1A │1  14│ VCC
1Y │2  13│ 6A
2A │3  12│ 6Y
2Y │4  11│ 5A
3A │5  10│ 5Y
3Y │6   9│ 4A
GND│7   8│ 4Y
   └─────┘
```

### IC 7408 - Quad 2-Input AND Gate
- **Function:** Outputs HIGH only when both inputs are HIGH
- **Package:** 14-pin DIP
- **Supply Voltage:** 5V (Pin 14: VCC, Pin 7: GND)
- **Gates per IC:** 4 AND gates
- **Usage in project:** Implement AND operations in Boolean expressions

**Pin Configuration:**
```
    7408
   ┌──┴──┐
1A │1  14│ VCC
1B │2  13│ 4B
1Y │3  12│ 4A
2A │4  11│ 4Y
2B │5  10│ 3B
2Y │6   9│ 3A
GND│7   8│ 3Y
   └─────┘
```

### IC 7432 - Quad 2-Input OR Gate
- **Function:** Outputs HIGH when either input is HIGH
- **Package:** 14-pin DIP
- **Supply Voltage:** 5V (Pin 14: VCC, Pin 7: GND)
- **Gates per IC:** 4 OR gates
- **Usage in project:** Implement OR operations in Boolean expressions

**Pin Configuration:**
```
    7432
   ┌──┴──┐
1A │1  14│ VCC
1B │2  13│ 4B
1Y │3  12│ 4A
2A │4  11│ 4Y
2B │5  10│ 3B
2Y │6   9│ 3A
GND│7   8│ 3Y
   └─────┘
```

### L298N Motor Driver Module
- **Function:** Controls DC motor speed and direction
- **Input Voltage:** 5-35V (typically 7-12V for this project)
- **Output Current:** Up to 2A per channel
- **Channels:** 2 (can control 2 motors independently)
- **Enable Pins:** ENA, ENB (for PWM speed control)

**Pin Functions:**
- **IN1, IN2:** Control left motor direction
- **IN3, IN4:** Control right motor direction
- **ENA:** Enable left motor (PWM input)
- **ENB:** Enable right motor (PWM input)
- **12V:** Power input from battery
- **GND:** Ground
- **5V:** 5V output (can power logic circuit)

### IR Sensor Modules
- **Type:** Infrared obstacle/line detection sensor
- **Detection Range:** 2-30cm (adjustable)
- **Output:** Digital (HIGH/LOW)
- **Operating Voltage:** 3.3-5V
- **Detection Principle:** 
  - Emits IR light
  - Detects reflection
  - White surface = High reflection = Output HIGH
  - Black line = Low reflection = Output LOW 

**Pins:**
- **VCC:** 5V power input
- **GND:** Ground
- **OUT:** Digital output to logic circuit

---

## Logic Circuit Design

### Boolean Expressions (from K-map simplification)

From the [logic-truth-tables.md](logic-truth-tables.md), we have:

| Output | Expression | Implementation |
|--------|-----------|----------------|
| LM1 | `I1 + I1'I3'` | 1 OR gate, 1 AND gate, 1 NOT gate |
| LM2 | `I1'I3` | 1 AND gate, 1 NOT gate |
| RM1 | `I1' + I1I3` | 1 OR gate, 1 AND gate, 1 NOT gate |
| RM2 | `I1I3'` | 1 AND gate, 1 NOT gate |

### Gate Count Summary

**Total gates required:**
- NOT gates: 5 (3 for sensor inversion + 2 for expressions) → 1× IC 7404
- AND gates: 4 → 1× IC 7408
- OR gates: 2 → 1× IC 7432

### Circuit Implementation Steps

#### Step 1: Sensor Signal Inversion
```
Sensor I1 → [NOT] → I1' (inverted)
Sensor I2 → [NOT] → I2' (inverted) [may not be needed in logic]
Sensor I3 → [NOT] → I3' (inverted)
```

#### Step 2: Intermediate Logic Signals
```
I1' AND I3' → Signal A
I1' AND I3  → Signal B (this is LM2)
I1 AND I3   → Signal C
I1 AND I3'  → Signal D (this is RM2)
```

#### Step 3: Final Output Signals
```
I1 OR Signal A  → LM1
Signal B        → LM2
I1' OR Signal C → RM1
Signal D        → RM2
```

---

## Motor Driver Configuration

### L298N Connection to Logic Circuit

The logic circuit outputs control the motor driver inputs:

| Logic Output | L298N Pin | Function |
|--------------|-----------|----------|
| LM1 | IN1 | Left motor control 1 |
| LM2 | IN2 | Left motor control 2 |
| RM1 | IN3 | Right motor control 1 |
| RM2 | IN4 | Right motor control 2 |

### Motor Direction Control

The L298N uses an H-bridge configuration to control motor direction:

**Left Motor (M1):**
- **IN1=1, IN2=0** → Motor spins forward
- **IN1=0, IN2=1** → Motor spins backward
- **IN1=0, IN2=0** → Motor stops
- **IN1=1, IN2=1** → Motor brake (not used)

**Right Motor (M2):**
- **IN3=1, IN4=0** → Motor spins forward
- **IN3=0, IN4=1** → Motor spins backward
- **IN3=0, IN4=0** → Motor stops
- **IN3=1, IN4=1** → Motor brake (not used)

### PWM Speed Control

The ENA and ENB pins accept PWM signals to control motor speed:
- **100% duty cycle** → Full speed
- **75% duty cycle** → 75% speed (recommended)
- **0% duty cycle** → Motor off

**Implementation:**
- Connect PWM module output to ENA (left motor enable)
- Connect PWM module output to ENB (right motor enable)
- Adjust duty cycle to optimize line-following smoothness

---

## Power Distribution

### Power Requirements

| Component | Voltage | Current (max) |
|-----------|---------|---------------|
| Logic ICs (7404, 7408, 7432) | 5V | ~10mA each |
| IR Sensors (3×) | 5V | ~30mA each |
| L298N Logic | 5V | ~30mA |
| DC Motors (2×) | 6-12V | 500mA-2A each |

### Power Supply Configuration

**Option 1: Single Battery with Voltage Regulator**
```
Battery (7.4-12V)
    ↓
Master Switch
    ↓
    ├──→ L298N 12V Input (Motors)
    │
    └──→ 5V Regulator
           ↓
           ├──→ Logic ICs
           ├──→ IR Sensors
           └──→ L298N 5V Input
```

### Common Ground

**CRITICAL:** All components must share a common ground:
- Battery GND
- Logic IC GND (Pin 7 on all ICs)
- IR Sensor GND
- L298N GND
- Motor GND (through L298N)

---

## Wiring Diagrams

### Main Circuit Schematic

```
                    +5V                    +12V
                     │                      │
    ┌────────────────┼──────────────────────┼────────┐
    │                │                      │        │
    │           ┌────┴────┐          ┌─────┴─────┐  │
    │   Sensor1 │ 7404    │          │   L298N   │  │
    ├───────────┤  NOT    │          │   Motor   │  │
    │   Sensor2 │ Gates   │          │   Driver  │  │
    ├───────────┤         │          │           │  │
    │   Sensor3 │         │  Logic   │ IN1 ← LM1 │  │
    │           └────┬────┘  Gates   │ IN2 ← LM2 │  │
    │                │     ┌────┐    │ IN3 ← RM1 │  │
    │                ├─────┤7408├────┤ IN4 ← RM2 │  │
    │                │     └────┘    │           │  │
    │                │     ┌────┐    │ ENA ← PWM │  │
    │                └─────┤7432├────┤ ENB ← PWM │  │
    │                      └────┘    │           │  │
    │                                │ M1   M2   │  │
    │                                └─┬─────┬───┘  │
    │                                  │     │      │
    │                              ┌───┴─┐ ┌─┴───┐  │
    │                              │Left │ │Right│  │
    │                              │Motor│ │Motor│  │
    │                              └─────┘ └─────┘  │
    │                                               │
    └───────────────── GND ─────────────────────────┘
```

---

## Pin Connections

### Detailed Pin-by-Pin Connections

#### IC 7404 (NOT Gates) - Input Inverters

| Pin | Connection | Purpose |
|-----|------------|---------|
| 1 | Sensor I1 OUT | Input from left sensor |
| 2 | To logic circuit | I1' output |
| 3 | Sensor I3 OUT | Input from right sensor |
| 4 | To logic circuit | I3' output |
| 5 | (Reserve) | Additional NOT if needed |
| 6 | (Reserve) | Additional NOT if needed |
| 7 | GND | Ground |
| 14 | +5V | Power |

#### IC 7408 (AND Gates)

| Gate | Input A | Input B | Output | Purpose |
|------|---------|---------|--------|---------|
| 1 | I1' | I3' | Signal A | For LM1 |
| 2 | I1' | I3 | LM2 | Left motor reverse |
| 3 | I1 | I3 | Signal C | For RM1 |
| 4 | I1 | I3' | RM2 | Right motor reverse |

**Pin Numbers:**
- Pins 7 = GND
- Pin 14 = +5V

#### IC 7432 (OR Gates)

| Gate | Input A | Input B | Output | Purpose |
|------|---------|---------|--------|---------|
| 1 | I1 | Signal A | LM1 | Left motor forward |
| 2 | I1' | Signal C | RM1 | Right motor forward |

**Pin Numbers:**
- Pin 7 = GND
- Pin 14 = +5V

#### L298N Motor Driver

| Pin | Connection | Description |
|-----|------------|-------------|
| IN1 | LM1 (from IC 7432) | Left motor control 1 |
| IN2 | LM2 (from IC 7408) | Left motor control 2 |
| IN3 | RM1 (from IC 7432) | Right motor control 1 |
| IN4 | RM2 (from IC 7408) | Right motor control 2 |
| ENA | PWM module | Left motor enable |
| ENB | PWM module | Right motor enable |
| 12V | Battery + | Power input |
| GND | Battery - | Ground |
| OUT1 | Left motor + | Left motor positive |
| OUT2 | Left motor - | Left motor negative |
| OUT3 | Right motor + | Right motor positive |
| OUT4 | Right motor - | Right motor negative |
| 5V | To logic circuit +5V rail | Regulated 5V output |

#### IR Sensors

**Left Sensor (I1):**
- VCC → +5V rail
- GND → GND rail
- OUT → IC 7404 Pin 1

**Center Sensor (I2):**
- VCC → +5V rail
- GND → GND rail
- OUT → (May not be used in simplified logic)

**Right Sensor (I3):**
- VCC → +5V rail
- GND → GND rail
- OUT → IC 7404 Pin 3
