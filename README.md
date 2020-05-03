# IOT-based-Warehouse-Monitoring
IOT
/*
********************************************
14CORE ULTRASONIC DISTANCE SENSOR CODE TEST
********************************************
*/
#define TRIGGER 0
#define ECHO    2

// NodeMCU Pin D6 > TRIGGER | Pin D5 > ECHO

#define BLYNK_PRINT Serial    // Comment this out to disable prints and save space
#include <SPI.h>
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <SimpleTimer.h>
#include <DHT.h>

// You should get Auth Token in the Blynk App.
// Go to the Project Settings (nut icon).
char auth[] = "o5LfSfdq247S961tIwV8dWuD9LHppSnb";

// Your WiFi credentials.
// Set password to "" for open networks.
char ssid[] = "pop";
char pass[] = "pop12345";

#define DHTPIN 16          // Digital pin 0
// Uncomment whatever type you're using!
#define DHTTYPE DHT11     // DHT 11
//#define DHTTYPE DHT22   // DHT 22, AM2302, AM2321
//#define DHTTYPE DHT21   // DHT 21, AM2301

DHT dht(DHTPIN, DHTTYPE);
SimpleTimer timer;
// This function sends Arduino's up time every second to Virtual Pin (5).
// In the app, Widget's reading frequency should be set to PUSH. This means
// that you define how often to send data to Blynk App.
void sendSensor()
{
  float h = dht.readHumidity();
  float t = dht.readTemperature(); // or dht.readTemperature(true) for Fahrenheit

  if (isnan(h) || isnan(t)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }
  // You can send any value at any time.
  // Please don't send more that 10 values per second.
  Blynk.virtualWrite(V6, h);  //V5 is for Humidity
  Blynk.virtualWrite(V7, t);  //V6 is for Temperature
}
void setup() {
  
  Serial.begin (9600);
  Blynk.begin(auth, ssid, pass);
  pinMode(TRIGGER, OUTPUT);
  pinMode(ECHO, INPUT);
  pinMode(13, OUTPUT);
  pinMode(BUILTIN_LED, OUTPUT);
  dht.begin();

  // Setup a function to be called every second
  timer.setInterval(1000L, sendSensor);
}
void loop() {
  
  long duration, distance;
  digitalWrite(TRIGGER, LOW);  
  delayMicroseconds(2); 
  
  digitalWrite(TRIGGER, HIGH);
  delayMicroseconds(10); 
  
  digitalWrite(TRIGGER, LOW);
  duration = pulseIn(ECHO, HIGH);
  distance = (duration/2) / 29.1;

     if (distance > 28) {
    
    Blynk.virtualWrite(V8, 255);
      digitalWrite(13, HIGH);
    delay(3000);
    digitalWrite(13, LOW);
    Blynk.email("Shreyasshedge11@gmail.com", "Storage Alert", " Warning!! Storage 0% fill");
    Blynk.notify("Storage Alert -Storage 0% fill");
}
  else {
    Blynk.virtualWrite(V8, 0);
   
  }

      if (distance <= 25) {
    Blynk.virtualWrite(V0, 255);
}
  else {
    Blynk.virtualWrite(V0, 0);
   
  }

 if (distance <= 21) {
    Blynk.virtualWrite(V1, 255);
}
  else {
    Blynk.virtualWrite(V1, 0);
  }

   if (distance <= 17) {
    Blynk.virtualWrite(V2, 255);
}
  else {
    Blynk.virtualWrite(V2, 0);
  }

   if (distance <= 12) {
    Blynk.virtualWrite(V3, 255);
}
  else {
    Blynk.virtualWrite(V3, 0);
  }

   if (distance <= 8) {
    
    Blynk.virtualWrite(V4, 255);
    digitalWrite(13, HIGH);
    delay(3000);
    digitalWrite(13, LOW);
    Blynk.email("Shreyasshedge11@gmail.com", "Storage Alert", " Warning!! Storage 100% filled");
    Blynk.notify("Storage Alert -Storage 100% filled");
}
  else {
    Blynk.virtualWrite(V4, 0);
   
  }

  
  
  Serial.print(distance);
  Serial.println("Centimeter:");
  Blynk.virtualWrite(V5, distance);
  delay(200);
  Blynk.run();
  timer.run(); // Initiates SimpleTimer
}
