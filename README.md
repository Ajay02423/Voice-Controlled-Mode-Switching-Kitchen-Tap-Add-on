# Voice-Controlled Mode Switching Kitchen Tap Add-on

A smart water conservation solution that enables automatic mode switching of kitchen taps 
using voice commands. This project aims to promote responsible water usage while enhancing 
user convenience through voice control technology.

## Hardware Components
- 1 x Arduino UNO
- 1 x DFRobot Voice Recognition Module (DF2301Q)
- 1 x Servo Motor
- 1 x 18650 Lithium-Ion Battery
- 1 x TP4056 Charging Module
- 1 x On/Off Switch
- Water Tap Add-on Assembly
- Jumper Wires

## Pin Configuration
Voice Recognition Module:
- SDA → A4 (Arduino)
- SCL → A5 (Arduino)
- VCC → 5V (Arduino)
- GND → GND (Arduino)

Servo Motor:
- Signal → Pin 9 (Arduino)
- VCC → 5V (Arduino)
- GND → GND (Arduino)

Power Supply:
- Battery → TP4056 Module (IN+/IN-)
- TP4056 OUT+ → Switch → Arduino Vin
- TP4056 OUT- → Arduino GND

## Code 
// Include required libraries
#include "DFRobot_DF2301Q.h"
#include <Servo.h>
#include <Wire.h>

// Pin definitions
#define SERVO_PIN 9
#define I2C_SDA A4
#define I2C_SCL A5

// Mode angles for servo
#define SHOWER_MODE 0
#define NORMAL_MODE 90
#define JET_MODE 180

// Water savings percentages
const float SHOWER_SAVINGS = 22.75;
const float JET_SAVINGS = 21.49;
const float NORMAL_SAVINGS = 8.0;

// Initialize components
DFRobot_DF2301Q_I2C voiceModule;
Servo modeServo;

void setup() {
  // Initialize serial communication
  Serial.begin(115200);
  
  // Initialize I2C communication
  Wire.begin();
  
  // Initialize voice recognition module
  while (!voiceModule.begin()) {
    Serial.println("Voice module initialization failed!");
    delay(1000);
  }
  
  // Configure voice module
  voiceModule.setVolume(5);              // Set volume (0-7)
  voiceModule.setWakeTime(15);           // Set wake time in seconds
  voiceModule.enableWakeupRecognition(); // Enable wake-up feature
  
  // Initialize servo motor
  modeServo.attach(SERVO_PIN);
  modeServo.write(NORMAL_MODE); // Start in normal mode
  
  Serial.println("System initialized successfully!");
}

/*
 * Mode Control Functions
 * Each function handles mode switching and prints status
 */

void setShowerMode() {
  modeServo.write(SHOWER_MODE);
  Serial.println("Switched to Shower Mode");
  Serial.print("Water Savings: ");
  Serial.print(SHOWER_SAVINGS);
  Serial.println("%");
}

void setNormalMode() {
  modeServo.write(NORMAL_MODE);
  Serial.println("Switched to Normal Mode");
  Serial.print("Water Savings: ");
  Serial.print(NORMAL_SAVINGS);
  Serial.println("%");
}

void setJetMode() {
  modeServo.write(JET_MODE);
  Serial.println("Switched to Jet Mode");
  Serial.print("Water Savings: ");
  Serial.print(JET_SAVINGS);
  Serial.println("%");
}

/*
 * Main Loop
 * Continuously checks for voice commands and switches modes accordingly
 */
void loop() {
  // Check for voice commands
  uint8_t commandId = voiceModule.getCMDID();
  
  // Process commands if received
  if (commandId != 0) {
    switch (commandId) {
      case 1: // "Shower Mode" command
        setShowerMode();
        break;
        
      case 2: // "Normal Mode" command
        setNormalMode();
        break;
        
      case 3: // "Jet Mode" command
        setJetMode();
        break;
        
      default:
        Serial.println("Unknown command received");
        break;
    }
  }
  
  // Small delay to prevent too frequent checking
  delay(100);
}

/*
## Troubleshooting Guide

1. Voice commands not recognized:
   - Check voice module connections
   - Verify wake word was spoken
   - Adjust module volume
   - Check power supply voltage

2. Servo not moving:
   - Verify servo connections
   - Check mechanical linkages
   - Test servo manually with simple code
   - Verify power supply

3. Inconsistent operation:
   - Check battery charge level
   - Verify all connections are secure
   - Ensure ambient noise isn't too high
   - Check for mechanical obstructions

## Performance Adjustments

- Voice Recognition:
  * Adjust volume setting for environment
  * Modify wake time if needed
  * Train commands in quiet environment

- Servo Control:
  * Calibrate mode angles for your tap
  * Adjust movement speed if needed
  * Ensure smooth mechanical operation

## Future Improvements

1. Aeration System Integration
   - Add air-mixing capability
   - Further reduce water usage
   
2. Automated On/Off System
   - Implement touch-free operation
   - Add automatic shutoff timer
   
3. Mobile Application
   - Add Bluetooth control
   - Monitor water usage
   - Custom settings interface
   
4. Flow Sensing
   - Add flow rate measurement
   - Track water consumption
   - Generate usage analytics
*/
