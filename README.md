# Automatic Pergola System

## Overview

This project is an automated system designed to extend and retract a pergola based on rain detection. The system uses an Arduino Uno, a rain sensor, an L298N motor driver, and two 12V motors to control the movement of the pergola. When rain is detected, the pergola is pulled over the house to provide protection. When there is no rain, the pergola retracts to allow for an open space.

## Components

- **Arduino Uno**: The microcontroller used to process the rain sensor data and control the motor driver.
- **L298N Motor Driver**: Controls the direction and speed of the motors.
- **12V Motors**: Two motors used to extend and retract the pergola.
- **Rain Sensor**: Detects the presence of rain and sends data to the Arduino.
- **Breadboard and Jumper Wires**: For wiring the components together.

## Wiring Diagram

1. **Arduino to Motor Driver (L298N)**:
    - Motor 1:
        - `motor1pin1` (Arduino Pin 2) to `IN1` on L298N
        - `motor1pin2` (Arduino Pin 3) to `IN2` on L298N
    - Motor 2:
        - `motor2pin1` (Arduino Pin 4) to `IN3` on L298N
        - `motor2pin2` (Arduino Pin 5) to `IN4` on L298N
    - Speed Control:
        - `motorSpeedPin1` (Arduino Pin 9) to `ENA` on L298N
        - `motorSpeedPin2` (Arduino Pin 10) to `ENB` on L298N

2. **Arduino to Rain Sensor**:
    - Rain Sensor output to `A3` on Arduino

3. **Power Connections**:
    - Connect the 12V power supply to the motor driver.
    - Ensure common ground between the Arduino and motor driver.

## Arduino Code

The code for this project controls the pergola based on the rain sensor readings. The main states are `STOPPED`, `FORWARD` (to extend the pergola), and `REVERSE` (to retract the pergola).

```cpp
int motor1pin1 = 2;
int motor1pin2 = 3;
int motor2pin1 = 4;
int motor2pin2 = 5;

int motorSpeedPin1 = 9;
int motorSpeedPin2 = 10;

enum State {STOPPED, FORWARD, REVERSE};
State currentState = STOPPED;

void setup() {
  Serial.begin(9600);

  pinMode(motor1pin1, OUTPUT);
  pinMode(motor1pin2, OUTPUT);
  pinMode(motor2pin1, OUTPUT);
  pinMode(motor2pin2, OUTPUT);

  pinMode(motorSpeedPin1, OUTPUT); 
  pinMode(motorSpeedPin2, OUTPUT);
}

void loop() {
  int value = analogRead(A3);
  Serial.print("Value: ");
  Serial.println(value);

  switch (currentState) {
    case STOPPED:
      if (value < 700) {
        currentState = FORWARD;
        driveMotors(FORWARD);
        delay(700);
        stopMotors();
      } else if (value >= 700) {
        currentState = REVERSE;
        driveMotors(REVERSE);
        delay(3000);
        stopMotors();
      }
      break;

    case FORWARD:
      if (value >= 700) {
        currentState = STOPPED;
      }
      break;

    case REVERSE:
      if (value < 700) {
        currentState = STOPPED;
      }
      break;
  }
  
  delay(100);
}

void driveMotors(State direction) {
  analogWrite(motorSpeedPin1, 10);
  analogWrite(motorSpeedPin2, 10);

  if (direction == FORWARD) {
    digitalWrite(motor1pin1, LOW);
    digitalWrite(motor1pin2, HIGH);
    digitalWrite(motor2pin1, LOW);
    digitalWrite(motor2pin2, HIGH);
  } else if (direction == REVERSE) {
    digitalWrite(motor1pin1, HIGH);
    digitalWrite(motor1pin2, LOW);
    digitalWrite(motor2pin1, HIGH);
    digitalWrite(motor2pin2, LOW);
  }
}

void stopMotors() {
  analogWrite(motorSpeedPin1, 0);
  analogWrite(motorSpeedPin2, 0);
}
```

## Installation and Usage

1. **Assemble the Hardware**:
   - Connect the Arduino, motor driver, motors, and rain sensor according to the wiring diagram.
   - Ensure all connections are secure and the power supply is properly connected.

2. **Upload the Code**:
   - Open the Arduino IDE.
   - Copy and paste the provided code into the IDE.
   - Select the correct board (Arduino Uno) and port.
   - Upload the code to the Arduino.

3. **Operation**:
   - When the rain sensor detects rain (sensor value < 700), the pergola extends.
   - When the rain sensor does not detect rain (sensor value >= 700), the pergola retracts.
   - The system continuously monitors the sensor and adjusts the pergola position accordingly.

## Troubleshooting

- Ensure the rain sensor is properly connected and functioning.
- Verify the motor driver connections and ensure the motors are correctly wired.
- Check the serial monitor for sensor values to debug any issues.

## Future Improvements

- Add a manual override switch to control the pergola manually.
- Implement adjustable speed control for the motors.
- Integrate a sun sensor to adjust the pergola based on sunlight.

## License

This project is open-source and available under the MIT License.

---

By following this guide, you can set up and operate the automatic pergola system to protect your outdoor space from rain effectively. Enjoy your new automated pergola!
