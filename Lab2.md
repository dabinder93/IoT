# Lab 2

__Source__
* Wemos D1 mini Pins: http://escapequotes.net/wp-content/uploads/2016/02/d1-mini-esp8266-board-sh_fixled.jpg
* http://escapequotes.net/wp-content/uploads/2016/02/esp8266-wemos-d1-mini-pinout.png
* HTTP Request: https://codebender.cc/sketch:322905#HTTPS%20Request%20Wemos%20D1.ino

# Turn on Builtin LED when Button Triggers
__Code__

* Changed ledPin into LED_BUILTIN
* Change buttonPin to 14 (if it was connected to D5 on the Arduino)

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

## Setup IFTTT
* Login to IFTT

__New Applet__
* Choose Webhooks as Service
* Choose Trigger (Receive a web request)
* choose Event Name (button_clicked)
* choose action service (Twitter)
* login to twitter -> Select what it should post

__Button Trigger Hypernlink__
https://maker.ifttt.com/trigger/button_clicked/with/key/ENSbuR1k4hEdMpOHpOe9btm9ZJYik25GBKSDDQW2QV

# Button press + Request
Request to IFTTT -> Notification to Smartphone that Button has been pressed 

__Code__

```
#include <ESP8266HTTPClient.h>
#include <ESP8266WiFi.h>
const int buttonPin = 5;     //Pin D1
int buttonState = 0; 

void setup() {

  pinMode(LED_BUILTIN, OUTPUT);
  // initialize the pushbutton pin as an input:
  pinMode(buttonPin, INPUT);  
  Serial.begin(115200);                 //Serial connection
  WiFi.begin("iotempire-msck", "schima&klinch");   //WiFi connection

  while (WiFi.status() != WL_CONNECTED) {  //Wait for the WiFI connection completion 
    delay(500);
    Serial.println("Waiting for connection");
  }
  Serial.println("Connected");
}

void loop() {
  buttonState = digitalRead(buttonPin);
  Serial.println(buttonState);
  if (buttonState == LOW) {
     digitalWrite(LED_BUILTIN, LOW);
     if(WiFi.status()== WL_CONNECTED){   //Check WiFi connection status
       HTTPClient http;    //Declare object of class HTTPClient
       http.begin("http://maker.ifttt.com/trigger/button_clicked/with/key/ENSbuR1k4hEdMpOHpOe9btm9ZJYik25GBKSDDQW2QV");  //Twitter Hook
       http.addHeader("Content-Type", "text/plain");  //Specify content-type header
       int httpCode = http.POST("Message from ESP8266");   //Send the request
       String payload = http.getString();                  //Get the response payload
     
       Serial.println(httpCode);   //Print HTTP return code
       Serial.println(payload);    //Print request response payload
       http.end();  //Close connection
     }else{
        Serial.println("Error in WiFi connection");   
     }
  } else {
    // turn LED off:
    digitalWrite(LED_BUILTIN, HIGH);  //WHY HIGH?!?!?
  }
}
```

## Problems
* No Request sent when Button Pressed, though the Code from the Last Task was inserted into the current Exercise.
* to Check if the Button was pressed we have to check if buttonState == LOW not buttonState == HIGH, as we assumed before.
* to turn the LED OFF we also have to set LED_BUILTIN HIGH  (inverted)

# Probleme mit Arduino
* Arduino Netzteil Deffekt -> Neues Erhalten
* Arduino Connection unterbricht oft
* Arduino tot -> neuer Arduino <3
* Dennoch Probleme beim Bespielen von Arduino (ca jeder 4 Versuch geht / nicht mehr)

__Solution:__
* Connect GND and D3  -> Flash a programm on it -> afterwards it can be used normally again

# LCD Display Exercise

## Setup
* Download Library from https://github.com/agnunez/ESP8266-I2C-LCD1602
* Create a new folder called "LiquidCrystal_I2C" under the folder named "libraries" in the Arduino sketchbook folder. 
* Create the folder "libraries" in case it does not exist yet. Place all the files in the "LiquidCrystal_I2C" folder.
** Wireing
* Top to bottom:
  GND,
  VCC - 5V,
  SDA,
  SCL
* Use the new added Example as a base for the LCS Exercise (File -> Examples -> Liquid CrystalL2C -> HelloWorld)

__I2C Display__  | __ESP8266__
------------- | -------------
GND	| GND
+5V	| +5V
SDA	| ESP8266 = GPIO 0 (Wemos etc. = D3)
SCL	| ESP8266 = GPIO 2 (Wemos etc. = D4)

__Code__

```
#include <ESP8266HTTPClient.h>
#include <ESP8266WiFi.h>
#include <Wire.h> 
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);
const int buttonPin = 5;     //Pin D1
int buttonState = 0; 

void setup() {

  pinMode(LED_BUILTIN, OUTPUT);
  // initialize the pushbutton pin as an input:
  pinMode(buttonPin, INPUT);
  lcd.begin(0,2);  // sda=0, scl=2
  lcd.backlight();
  Serial.begin(115200);                 //Serial connection
  WiFi.begin("iotempire-msck", "schima&klinch");   //WiFi connection
  while (WiFi.status() != WL_CONNECTED) {  //Wait for the WiFI connection completion
 
    delay(500);
    Serial.println("Waiting for connection");
  }

  Serial.println("Connected");
}

void loop() {

  buttonState = digitalRead(buttonPin);
  Serial.println(buttonState);
  if (buttonState == LOW) {
     digitalWrite(LED_BUILTIN, LOW);
     if(WiFi.status()== WL_CONNECTED){   //Check WiFi connection status
   
       HTTPClient http;    //Declare object of class HTTPClient

       http.begin("http://maker.ifttt.com/trigger/button_clicked/with/key/ENSbuR1k4hEdMpOHpOe9btm9ZJYik25GBKSDDQW2QV");  //Twitter Hook
       http.addHeader("Content-Type", "text/plain");  //Specify content-type header
     
       int httpCode = http.POST("Message from ESP8266");   //Send the request
       String payload = http.getString();                  //Get the response payload
     
       Serial.println(httpCode);   //Print HTTP return code
       Serial.println(payload);    //Print request response payload
     
       http.end();  //Close connection
 
     }else{
        Serial.println("Error in WiFi connection");   
        lcd.print("No WiFi!");
     }
      lcd.print("Event Triggert!");

  // Turn on the blacklight and print a message.
  } else {
    // turn LED off:
    digitalWrite(LED_BUILTIN, HIGH);  //WHY HIGH?!?!? But OK :)
  }
}

```



