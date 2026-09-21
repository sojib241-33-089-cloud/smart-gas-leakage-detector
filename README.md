# smart-gas-leakage-detector
#include <Servo.h>

// Pin Definitions
const int MQ2_PIN = A0;      // MQ-2 sensor analog pin connected to A0
const int BUZZER_PIN = 8;    // Buzzer connected to Digital Pin 8
const int LED_PIN = 9;       // Alert LED connected to Digital Pin 9
const int SERVO_PIN = 10;    // Servo motor signal connected to Digital Pin 10

// Threshold Definition
// Adjust this value based on your environment and sensor calibration
const int GAS_THRESHOLD = 400; 

// Servo Positions
const int VALVE_OPEN_ANGLE = 0;    // Normal state: Valve Open
const int VALVE_CLOSED_ANGLE = 90; // Alert state: Valve Closed

Servo gasValveServo;

void setup() {
  // Serial Monitoring
  Serial.begin(9600);

  // Pin Modes Configuration
  pinMode(MQ2_PIN, INPUT);
  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(LED_PIN, OUTPUT);

  // Attach Servo Motor
  gasValveServo.attach(SERVO_PIN);
  
  // Initial Safety Reset
  gasValveServo.write(VALVE_OPEN_ANGLE);
  digitalWrite(BUZZER_PIN, LOW);
  digitalWrite(LED_PIN, LOW);

  Serial.println("System Initialized. Monitoring gas levels...");
}

void loop() {
  // Read analog value from MQ-2 sensor
  int sensorValue = analogRead(MQ2_PIN);

  // Display value in Serial Monitor
  Serial.print("Current Gas Level: ");
  Serial.println(sensorValue);

  // Check if gas concentration exceeds safe threshold
  if (sensorValue >= GAS_THRESHOLD) {
    // Trigger Alarm & Safety Action
    digitalWrite(BUZZER_PIN, HIGH);
    digitalWrite(LED_PIN, HIGH);
    gasValveServo.write(VALVE_CLOSED_ANGLE); // Rotate servo to turn off gas valve
    
    Serial.println("WARNING: Gas Leakage Detected! Valve Closed.");
  } 
  else {
    // Deactivate Alarm & Reset Safety System
    digitalWrite(BUZZER_PIN, LOW);
    digitalWrite(LED_PIN, LOW);
    gasValveServo.write(VALVE_OPEN_ANGLE);  // Return valve to normal position
  }

  // Small delay for stable reading
  delay(500);
}
