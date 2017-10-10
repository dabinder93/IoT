# Lab 2

__Source__
* Wemos D1 mini Pins: http://escapequotes.net/wp-content/uploads/2016/02/d1-mini-esp8266-board-sh_fixled.jpg
* HTTP Request: https://codebender.cc/sketch:322905#HTTPS%20Request%20Wemos%20D1.ino

__Code__

Changed ledPin in to LED_BUILTIN
Change buttonPin to 14 

```
/*
  Button

  Turns on and off a light emitting diode(LED) connected to digital pin 13,
  when pressing a pushbutton attached to pin 2.

  The circuit:
  - LED attached from pin 13 to ground
  - pushbutton attached to pin 2 from +5V
  - 10K resistor attached to pin 2 from ground

  - Note: on most Arduinos there is already an LED on the board
    attached to pin 13.

  created 2005
  by DojoDave <http://www.0j0.org>
  modified 30 Aug 2011
  by Tom Igoe

  This example code is in the public domain.

  http://www.arduino.cc/en/Tutorial/Button
*/

// constants won't change. They're used here to set pin numbers:
const int buttonPin = 14;     // the number of the pushbutton pin
//const int ledPin =  13;      // the number of the LED pin

// variables will change:
int buttonState = 0;         // variable for reading the pushbutton status

void setup() {
  // initialize the LED pin as an output:
  pinMode(LED_BUILTIN, OUTPUT);
  // initialize the pushbutton pin as an input:
  pinMode(buttonPin, INPUT);
}

void loop() {
  // read the state of the pushbutton value:
  buttonState = digitalRead(buttonPin);

  // check if the pushbutton is pressed. If it is, the buttonState is HIGH:
  if (buttonState == HIGH) {
    // turn LED on:
    digitalWrite(LED_BUILTIN, HIGH);
  } else {
    // turn LED off:
    digitalWrite(LED_BUILTIN, LOW);
  }
}
```

Next step is to combine this with the WLAN Example and Post a request to IFTTT

# Setup IFTTT
- Login to IFTT
** New Applet
- Choose Webhooks as Service
- Choose Trigger (Receive a web request)
- choose Event Name (button_clicked)
- choose action service (Twitter)
- login to twitter -> Select what it should post

## Button press Request
Request to IFTTT -> Notification to Smartphone that Button has been pressed 


# Probleme
* No Request sent when Button Pressed, though the Code from the Last Task was inserted into the current Exercise.

# Probleme mit Arduino
* Arduino Netzteil Deffekt -> Neues Erhalten
* Arduino Connection unterbricht oft
* Arduino tot -> neuer Arduino <3
* Dennoch Probleme beim Bespielen von Arduino (ca jeder 4 Versuch geht / nicht mehr)

Problemlösung:
* Connect GND and D3  -> Flash a programm on it -> afterwards it can be used normally again

** Code



