# Line-Following-Robot-Using-Logic-Gates
A hardware-based line following robot that uses logic gates (AND, OR, NOT, NAND, NOR, XOR) instead of microcontrollers for decision-making and motor control.
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

*[Add your circuit diagram image here]*

## Demo (Photos/Videos)

*[Add a GIF or video of your robot in action]*

## Setup/Build Instructions

*[Manual]*

## Team members / credits

*[Members]*
Mary Marghareth Bueno
Norvel Ian Gendoy
John Dio Lumacang
Methuselah Noreen Presbitero
Cyril Josef Tinae

## License

*[Add your license information here]*
