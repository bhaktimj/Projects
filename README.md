# Industrial Automation and Monitoring System using RFID with Access Control and Temperature Monitoring

## Project Description.

An industrial automation and monitoring system that integrates RFID-based access control with temperature monitoring, safety monitoring, and energy-saving automation.

## Main Features

- RFID-based access control
- Temperature and environmental monitoring
- Industrial safety and hazard detection
- Energy-saving automation based on occupancy
- Emergency stop and safety override
- STM32-based control nodes
- ESP8266-based communication and alert gateway

## Technologies Used

- STM32F103C8T6
- ESP8266 NodeMCU
- MFRC522 RFID Reader
- DHT11 Temperature Sensor
- MQ-4 Gas Sensor
- IR Flame Sensor
- PIR Sensors
- 4×4 Matrix Keypad
- LCD Display
- Relay Module
- Servo Motor

## Project Documentation

The complete project documentation and source-code blueprint are available in this repository.

## Team Project

Developed as an academic project for Electrical and Electronics Engineering.

## Mini Project

### Smart Fan (PIR + DHT11)

A small Arduino-based smart fan system that uses a PIR sensor for motion detection and a DHT11 sensor for temperature monitoring.

The fan operates at different PWM speeds based on temperature when motion is detected:

- Fan OFF below 25°C
- Medium speed at 25–27°C
- High speed at 28°C and above
- Fan automatically turns OFF after 10 seconds without detected motion

The implementation uses non-blocking timing with `millis()` for motion and temperature monitoring.

Source code: [`mini-project.md`](mini-project.md)
