/*
 * ============================================
 * Reliable Smart Fan (PIR + DHT11)
 * ============================================
 *
 * This code is reliable and non-blocking.
 * It uses a DHT11 for temperature.
 */

#include "DHT.h"

// ---------------- PIN DEFINITIONS ----------------
#define DHT_PIN 2       // DHT11 data pin
#define FAN_PIN 9       // PWM pin for MOSFET gate
#define PIR_PIN 7       // PIR motion sensor output pin

// ---------------- DHT SENSOR CONFIG --------------
//
// THIS IS THE ONLY LINE YOU NEED TO CHANGE!
//
#define DHT_TYPE DHT11   // Was DHT22, now it's DHT11
//
// -------------------------------------------------

DHT dht(DHT_PIN, DHT_TYPE);

// ---------------- THRESHOLDS ---------------------
// NOTE: The DHT11 is less accurate than the DHT22.
// You might need to adjust these values by 1 or 2 degrees.
const int TEMP_MED   = 25;  // Medium speed threshold (°C)
const int TEMP_HIGH  = 28;  // High speed threshold (°C)

// ---------------- TIMING SETTINGS ----------------
const unsigned long PIR_HOLD_TIME      = 10000; // 10 seconds fan hold time
const unsigned long TEMP_READ_INTERVAL = 2000;  // Read temperature every 2 seconds

// ---------------- STATE VARIABLES ----------------
unsigned long lastMotionTime = 0;
unsigned long lastTempReadTime = 0;

float currentTemp = 0.0;
int fanSpeed = 0;
int lastFanSpeed = -1; // Set to -1 to force an update on first loop

// -------------------- SETUP --------------------
void setup() {
  Serial.begin(9600);
  Serial.println("Smart Fan System Initializing...");

  pinMode(FAN_PIN, OUTPUT);
  pinMode(PIR_PIN, INPUT);

  dht.begin();
  analogWrite(FAN_PIN, 0); // Start with fan OFF

  Serial.println("System Ready. Waiting for motion...");
}

// -------------------- MAIN LOOP --------------------
void loop() {
  unsigned long now = millis();

  // --- 1. PIR MOTION CHECK ---
  if (digitalRead(PIR_PIN) == HIGH) {
    lastMotionTime = now; // Reset the hold timer
  }
  
  bool motionActive = (now - lastMotionTime) < PIR_HOLD_TIME;

  // --- 2. TEMPERATURE READ (Non-blocking) ---
  // The DHT11 needs at least 1-2 seconds between readings.
  // This 2000ms interval is perfect.
  if (now - lastTempReadTime >= TEMP_READ_INTERVAL) {
    lastTempReadTime = now; // Reset the temp timer

    float t = dht.readTemperature();
    if (!isnan(t)) { // Check if the read was successful
      currentTemp = t;
      Serial.print("Sensor Read: ");
      Serial.print(currentTemp);
      Serial.println(" *C");
    } else {
      Serial.println("Failed to read from DHT11!");
    }
  }

  // --- 3. FAN LOGIC ---
  if (motionActive) {
    // A person is here (or was here recently)
    if (currentTemp >= TEMP_HIGH) {
      fanSpeed = 255; // High speed
    } else if (currentTemp >= TEMP_MED) {
      fanSpeed = 170; // Medium speed
    } else {
      fanSpeed = 0;   // Too cool
    }
  } else {
    // No motion for a while, turn fan off
    fanSpeed = 0;
  }

  // --- 4. APPLY FAN SPEED ---
  if (fanSpeed != lastFanSpeed) {
    
    analogWrite(FAN_PIN, fanSpeed);
    lastFanSpeed = fanSpeed; // Update the state

    // Print the new status
    if (fanSpeed == 255) {
      Serial.println("-> Fan: HIGH");
    } else if (fanSpeed == 170) {
      Serial.println("-> Fan: MEDIUM");
    } else {
      Serial.println("-> Fan: OFF");
    }
  }
}
