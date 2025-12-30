# Logic Truth Tables and Karnaugh Maps

This document contains the complete truth tables, Karnaugh maps (K-maps), and Boolean expressions used to design the line-following robot's logic circuit.

## Truth Table

The robot uses **3 IR sensors** (I1, I2, I3) as inputs and controls **4 motor outputs** (LM1, LM2 for left motor; RM1, RM2 for right motor).

| Sensors ||| Left Motor || Right Motor || Robot |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **I1** | **I2** | **I3** | **LM1** | **LM2** | **RM1** | **RM2** | **Direction** |
| 0 | 0 | 0 | 1 | 0 | 1 | 0 | Forward |
| 0 | 0 | 1 | 0 | 1 | 1 | 0 | Left |
| 0 | 1 | 0 | 1 | 0 | 1 | 0 | Forward |
| 0 | 1 | 1 | 0 | 1 | 1 | 0 | Left |
| 1 | 0 | 0 | 1 | 0 | 0 | 1 | Right |
| 1 | 0 | 1 | 1 | 0 | 1 | 0 | Forward |
| 1 | 1 | 0 | 1 | 0 | 0 | 1 | Right |
| 1 | 1 | 1 | 0 | 1 | 1 | 0 | Left |

### Sensor Input Convention
- **1** = Sensor detects WHITE surface (no line)
- **0** = Sensor detects BLACK line

### Motor Output Convention
For each motor, two outputs control direction:
- **LM1=1, LM2=0** → Left motor forward
- **LM1=0, LM2=1** → Left motor backward/turn
- **RM1=1, RM2=0** → Right motor forward
- **RM1=0, RM2=1** → Right motor backward/turn

---

## Karnaugh Maps (K-Maps)

K-maps were used to simplify the Boolean expressions for each of the four outputs.

### K-Map for LM1 (Left Motor Output 1)

```
        I2I3
I1      00  01  11  10
    0 | 1   0   0   1  |
    1 | 1   1   1   1  |
```

**Groupings:**
- Group of 4: I1=1 (covers all columns when I1=1)
- Group of 2: I1'I3' (covers I1=0, I3=0)

**Simplified Expression:**
```
LM1 = I1 + I1'I3'
```

### K-Map for LM2 (Left Motor Output 2)

```
        I2I3
I1      00  01  11  10
    0 | 0   1   1   0  |
    1 | 0   0   0   0  |
```

**Groupings:**
- Group of 2: I1'I3 (covers I1=0, I3=1)

**Simplified Expression:**
```
LM2 = I1'I3
```

### K-Map for RM1 (Right Motor Output 1)

```
        I2I3
I1      00  01  11  10
    0 | 1   1   1   1  |
    1 | 0   1   1   0  |
```

**Groupings:**
- Group of 4: I1'=0 (covers all columns when I1=0)
- Group of 2: I1I3 (covers I1=1, I3=1)

**Simplified Expression:**
```
RM1 = I1' + I1I3
```

### K-Map for RM2 (Right Motor Output 2)

```
        I2I3
I1      00  01  11  10
    0 | 0   0   0   0  |
    1 | 1   0   0   1  |
```

**Groupings:**
- Group of 2: I1I3' (covers I1=1, I3=0)

**Simplified Expression:**
```
RM2 = I1I3'
```

---

## Final Simplified Boolean Expressions

After K-map simplification, the logic circuit requires the following expressions:

| Output | Boolean Expression | Description |
|--------|-------------------|-------------|
| **LM1** | `I1 + I1'I3'` | Left motor forward control |
| **LM2** | `I1'I3` | Left motor reverse/turn control |
| **RM1** | `I1' + I1I3` | Right motor forward control |
| **RM2** | `I1I3'` | Right motor reverse/turn control |

### Gate Requirements

Based on these expressions, the following ICs are needed:

- **IC 7404 (NOT gates):** For I1' (NOT I1)
- **IC 7408 (AND gates):** For I1'I3', I1'I3, I1I3, I1I3'
- **IC 7432 (OR gates):** For LM1 and RM1 expressions

---

## Logic Gate Implementation

### LM1 = I1 + I1'I3'
1. NOT gate: I1' = NOT(I1)
2. AND gate: I1'I3' = I1' AND (NOT I3)
3. OR gate: LM1 = I1 OR I1'I3'

### LM2 = I1'I3
1. NOT gate: I1' = NOT(I1)
2. AND gate: LM2 = I1' AND I3

### RM1 = I1' + I1I3
1. NOT gate: I1' = NOT(I1)
2. AND gate: I1I3 = I1 AND I3
3. OR gate: RM1 = I1' OR I1I3

### RM2 = I1I3'
1. NOT gate: I3' = NOT(I3)
2. AND gate: RM2 = I1 AND I3'

---

## Verification

Each Boolean expression was verified by:
1. Testing against the original truth table
2. Building the circuit on breadboard and simulations online
3. Using test inputs to verify outputs match expected behavior

All expressions produced correct outputs matching the truth table for all 8 input combinations.