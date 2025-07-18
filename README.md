# latching-power-switch-arduino-circuit
An Arduino-based latching power switch with auto-off functionality. Press the button to toggle ON/OFF, and the system automatically shuts off after 10 seconds.

## Project Description:
Auto Latching Power Switch with Auto-Off Timer using Arduino.
This project demonstrates a simple Arduino-based latching power switch. When the button is pressed, the output turns ON. After 10 seconds, the output turns OFF automatically. Pressing the button again will toggle the power state.
It’s useful for devices that should automatically shut off after a limited time — such as small appliances, DIY tools, or safety timers.

## Components Used:
| Component                    | Quantity  |
| ---------------------------- | --------- |
| Arduino Uno                  | 1         |
| Push Button                  | 1         |
| LED                          | 1         |
| 220Ω Resistor (for LED)      | 1         |
| MOSFET (nMOS)                | 1         |
| Breadboard                   | 1         |
| Jumper Wires                 | as needed |

## Wiring Instructions:

1. **Push Button:**
  * Pin 1B connected to GND
  * Pin 2B connected to Arduino digital pin D2

2. **NMOS Transistor:**
  * **Source** connected to GND
  * **Gate** connected to Arduino digital pin D3
  * **Drain** connected to LED cathode through a 220Ω resistor

3. **LED:**
  * **Cathode** connected to NMOS Drain via resistor
  * **Anode** connected to +5V (power supply positive)

## Arduino Code:
```cpp
const int buttonPin = 2;      // Push button connected to pin D2
const int ledPin = 3;         // NMOS Gate connected to pin D3 (controls LED)

bool isOn = false;            // Current power state
unsigned long startTime = 0;  // Time when the output was turned ON
unsigned long autoOffDelay = 10000; // Auto-off delay in milliseconds (10 seconds)

int lastButtonState = HIGH;   // Previous button state
int buttonState;              // Current button state
unsigned long lastDebounceTime = 0; // Last debounce time
unsigned long debounceDelay = 50;   // Debounce delay time

void setup() {
  pinMode(buttonPin, INPUT_PULLUP); // Use internal pull-up resistor for the button
  pinMode(ledPin, OUTPUT);           // Set LED pin as output
  digitalWrite(ledPin, LOW);         // Ensure LED is off initially
}

void loop() {
  int reading = digitalRead(buttonPin); // Read the button state

  // Check if the button state changed (for debounce)
  if (reading != lastButtonState) {
    lastDebounceTime = millis();
  }

  // If enough time has passed, consider the reading stable
  if ((millis() - lastDebounceTime) > debounceDelay) {
    // If button state changed
    if (reading != buttonState) {
      buttonState = reading;

      // If button is pressed (LOW because of pull-up)
      if (buttonState == LOW) {
        isOn = !isOn; // Toggle the power state

        if (isOn) {
          digitalWrite(ledPin, HIGH);  // Turn ON the LED (activate MOSFET)
          startTime = millis();        // Reset the timer
        } else {
          digitalWrite(ledPin, LOW);   // Turn OFF the LED (deactivate MOSFET)
        }
      }
    }
  }

  lastButtonState = reading;

  // Automatically turn off after delay
  if (isOn && (millis() - startTime >= autoOffDelay)) {
    isOn = false;
    digitalWrite(ledPin, LOW);
  }
}
```
**you can tinker this project from [here](https://www.tinkercad.com/things/da8GeKwzczh-latching-power-switch-arduino-circuit)**
