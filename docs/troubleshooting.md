# Troubleshooting Guide

This document contains common issues encountered during the development and testing of the line-following robot, along with their solutions.

---

## Issue #1: Reversed Motor Outputs

### Problem Description
During initial testing, the robot's motor responses were inverted from the expected behavior:
- When it should turn left, it turned right
- When it should turn right, it turned left
- Forward movement was also affected

### Root Cause
The IR sensor outputs had opposite polarity from what the logic circuit expected. The sensors were outputting:
- LOW (0) when detecting white surface
- HIGH (1) when detecting black line

But the circuit was designed expecting:
- HIGH (1) for white surface
- LOW (0) for black line

### Solution
**Added NOT gates (inverters) to all sensor inputs**

1. Connected each IR sensor output to a 7404 NOT gate input
2. Used the inverted outputs as inputs to the main logic circuit
3. This effectively swapped the sensor logic to match the circuit design

### Implementation
```
Original:  IR Sensors → Logic Circuit
Fixed:     IR Sensors → NOT Gates → Logic Circuit
```

**Additional NOT Gates Required:** 3 (one for each sensor: I1, I2, I3)

### Verification
After adding the inverters:
- Tested on straight black line → Robot moved forward ✓
- Tested with left sensor on black → Robot turned left ✓
- Tested with right sensor on black → Robot turned right ✓

---

## Issue #2: Excessive Wiggling on Straight Lines

### Problem Description
When the robot was placed on a straight line, it exhibited unstable behavior:
- Oscillated left and right continuously
- Could not maintain smooth forward movement
- Made the line-following unpredictable and jerky

### Root Cause
The motor speed was too fast relative to the sensor response time. The robot would:
1. Detect slight deviation from center
2. Correct too aggressively due to high speed
3. Overshoot to the other side
4. Repeat the cycle, creating oscillation

**Think of it like:** Steering a car at high speed on a narrow road - small corrections become exaggerated.

### Solution
**Implemented PWM (Pulse Width Modulation) to reduce motor speed**

1. Connected PWM module to the L298N motor driver's enable pins (ENA, ENB)
2. Reduced the duty cycle to slow down motor rotation
3. Found optimal speed through iterative testing

### Recommended PWM Settings
- **Duty Cycle:** 60-75% of maximum speed
- **Frequency:** Standard motor control frequency (1-5 kHz)

### Benefits of Slower Speed
- Sensors have more time to detect line position
- Logic circuit has time to process and respond
- Smoother corrections without overshooting
- More stable overall behavior

### Verification
After speed reduction:
- Robot followed straight lines smoothly ✓
- Minimal oscillation observed ✓
- Predictable and controlled movement ✓

---

## Issue #3: Poor Performance on Curved Lines

### Problem Description
After the robot body/chassis design was finalized and assembled:
- Robot performed well on straight lines
- Failed to follow curved paths properly
- On curves, the robot would **spin in place** instead of following the line
- Robot appeared to be "searching" for the line instead of tracking it

### Root Cause Analysis
Multiple factors contributed to this issue:

1. **Sensor Placement:** After mounting sensors on the final chassis, the spacing and angle changed slightly
2. **Sensor Sensitivity:** Environmental factors (ambient light, surface reflection) affected detection threshold
3. **Sensor Height:** Distance from ground to sensors may have increased during final assembly
4. **Center of Gravity:** Weight distribution affected turning behavior

### Solution
**Recalibrated sensor placement and sensitivity**

#### Step 1: Sensor Position Adjustment
- Moved sensors closer together to reduce the "decision zone"
- Ensured sensors were level and at consistent height (2-5mm from ground)
- Positioned sensors slightly forward for earlier line detection

#### Step 2: Sensitivity Calibration
- Adjusted the potentiometers on each IR sensor module
- Calibration procedure:
  1. Place sensor over white surface
  2. Turn potentiometer until indicator LED just turns OFF
  3. Place sensor over black line
  4. Verify indicator LED turns ON
  5. Fine-tune for consistent detection

#### Step 3: Testing and Iteration
- Tested on gentle curves → adjusted if needed
- Tested on sharp curves → noted limitations
- Documented optimal sensor settings for future reference

### Sensor Placement Guidelines

**Optimal Configuration:**
```
        [Left]  [Center]  [Right]
           •       •         •
        <------ 30-40mm ------>
```

- **Spacing:** 30-40mm between outer sensors
- **Height:** 2-5mm from ground surface
- **Angle:** Sensors should face straight down (perpendicular to ground)

### Verification
After recalibration:
- Robot successfully followed gentle curves ✓
- Maintained line contact through turns ✓
- No more spinning behavior ✓
- Smooth transitions from straight to curved sections ✓

---

## Additional Common Issues

### Problem: Robot Doesn't Move at All

**Possible Causes:**
- Power switch is OFF
- Battery voltage too low (check with multimeter)
- Loose wire connections
- Motor driver not receiving power
- Logic circuit not powered

**Solutions:**
1. Check all power connections
2. Verify battery voltage (should be 7-12V depending on motors)
3. Test motors directly with battery to ensure they work
4. Check motor driver connections to battery and motors
5. Verify logic ICs are receiving power (VCC and GND)

---

### Problem: One Motor Works, Other Doesn't

**Possible Causes:**
- Faulty motor
- Loose connection to motor driver
- One side of logic circuit not working properly
- Motor driver channel failure

**Solutions:**
1. Swap motor connections at L298N to isolate problem
2. Check logic circuit outputs with LED or multimeter
3. Verify motor driver connections
4. Test suspected faulty motor with direct power

---

### Problem: Inconsistent Sensor Detection

**Possible Causes:**
- Ambient lighting interfering with IR sensors
- Sensors too far from ground
- Line surface not contrasting enough
- Dirty or dusty sensors

**Solutions:**
1. Recalibrate sensor sensitivity
2. Adjust sensor height (2-5mm is optimal)
3. Test in controlled lighting conditions
4. Clean sensor lenses
5. Use matte black line on white surface for best contrast

---

### Problem: Robot Works on Test Track but Not Elsewhere

**Possible Causes:**
- Different surface material (glossy vs. matte)
- Different line width
- Different lighting conditions
- Different floor texture

**Solutions:**
1. Recalibrate sensors for new environment
2. Adjust sensor sensitivity potentiometers
3. Test in similar lighting conditions
4. Ensure line width is consistent (recommended: 15-25mm)

---

## Prevention Tips

To avoid common issues:

- ✓ Use color-coded wires for organization
- ✓ Secure all connections firmly
- ✓ Label sensor positions (Left, Center, Right)
- ✓ Keep spare components on hand
- ✓ Test each subsystem before integration
- ✓ Document your working configuration
- ✓ Take photos of successful wiring setups

