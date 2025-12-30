# Line-Following-Robot-Using-Logic-Gates
A hardware-based line following robot that uses logic gates (AND, OR, NOT) instead of microcontrollers for decision-making and motor control.

## What the Robot Does

A line-following robot is an autonomous robot that moves by detecting and following a visible line on the floor (black line on a white surface). It continuously adjusts its direction to stay on the path without human control.

## Components and Materials

| Component | Purpose |
|-----------|---------|
| **Motor Chassis** | Holds the motors, wheels, and body of the robot |
| **Breadboard** | Used for assembling logic circuits without soldering |
| **Wires** | Connect all electronic components |
| **IC 7404 (NOT Gate)** | Inverts sensor signals |
| **IC 7408 (AND Gate)** | Combines logic conditions |
| **IC 7432 (OR Gate)** | Controls motor logic outputs |
| **L298N DC Motor Driver Module** | Drives and controls the DC motors |
| **IR Sensors** | Detect the line by sensing reflected infrared light |
| **Battery** | Supplies power to the circuit and motors |
| **Switch** | Turns the robot ON or OFF |

## How It Works

### 1. IR Sensor Detection

The IR sensors are placed near the ground:
- When the sensor is above a **white surface**, infrared light is reflected and detected
- When above a **black line**, less light is reflected, producing a different signal

### 2. Signal Processing Using Logic ICs

The sensor outputs are processed using logic gates:
- **7404 (NOT gate)** — Inverts signals when needed
- **7408 (AND gate)** — Decides when both conditions are true (e.g., move forward)
- **7432 (OR gate)** — Allows turning when either sensor detects the line

### 3. Motor Control

The logic gate outputs are sent to the **L298N motor driver**, which controls the direction and movement of the DC motors.

### 4. Movement Behavior

The robot follows these rules:
- **Both sensors detect white** → Robot moves forward
- **Left sensor detects black** → Robot turns left
- **Right sensor detects black** → Robot turns right

This continuous adjustment allows the robot to follow the line smoothly.

### 5. Power Control

- The **battery** supplies power to the entire system
- The **switch** allows the user to start or stop the robot easily

---

## Circuit Diagram

![Circuit Diagram](images/circuit-diagram.png)

*Detailed circuit schematics, truth tables, and K-maps are available in the [docs/](docs/) folder.*

## Demo (Photos/Videos)

![Robot Photo](images/robot-assembled.jpg)

*[Demo video will be added here]*

## Setup/Build Instructions

### Phase 1: Logic Design
1. Create the truth table for all sensor input combinations (3 sensors = 8 states)
2. Use Karnaugh Maps (K-maps) to simplify the Boolean expressions
3. Identify the required logic gates: IC 7404 (NOT), IC 7408 (AND), IC 7432 (OR)

### Phase 2: Assembly

**Step 1: Assemble the Chassis**
- Attach the DC motors to the motor chassis
- Mount the wheels to the motor shafts
- Secure the base plate

**Step 2: Build the Logic Circuit**
- Implement the simplified Boolean expressions on the breadboard
- Connect the ICs with proper power (VCC and GND)
- Add inverters (NOT gates) to the sensor inputs as needed
- Test each gate output before proceeding

**Step 3: Connect IR Sensors**
- Mount the IR sensors at the front of the robot, close to the ground
- Connect sensor outputs to the logic circuit inputs
- Test sensor detection on black and white surfaces

**Step 4: Connect the Motor Driver**
- Wire the logic circuit outputs to the L298N input pins
- Connect the DC motors to the L298N output terminals
- Connect PWM to the enable pins for speed control
- Wire the motor driver to the battery

**Step 5: Power Distribution**
- Install the master switch between the battery and circuit
- Distribute power to the logic ICs, motor driver, and IR sensors

**Step 6: Testing and Calibration**
- Test the robot on a straight line
- Adjust sensor sensitivity for proper line detection
- Fine-tune motor speed using PWM to prevent wiggling
- Test on curves and adjust sensor placement as needed

For detailed assembly instructions, see [docs/build-guide.md](docs/build-guide.md).

## Team Members / Credits

**Tonton's Malunggay Pandesal**

- Mary Maghareth Bueno
- Norvel Ian Gendoy
- John Dio Lumacang
- Methuselah Noreen Presbitero
- Cyril Josef Tinae

*CMSC 130 - Logic Design and Digital Computer Circuits*  
*University of the Philippines Cebu*  
*December 15, 2024*

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.