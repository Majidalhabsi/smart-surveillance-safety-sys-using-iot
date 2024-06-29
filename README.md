# smart-surveillance-safety-sys-using-iot
# MEC
 /* Fill-in information from Blynk Device Info here */

/* Fill-in information from Blynk Device Info here */
#define BLYNK_TEMPLATE_ID           "TMPL6HFgnAuIT"
#define BLYNK_TEMPLATE_NAME         "Quickstart Template"
#define BLYNK_AUTH_TOKEN            "O8Uom4K25p0mGi49dXxzxkjTFDfEpzD-"
/* Comment this out to disable prints and save space */
#define BLYNK_PRINT Serial

#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include "DHT.h"
#define DHTPIN D3     // Digital pin connected to the DHT sensor
#define DHTTYPE DHT22
#include<Servo.h>
#define trigPin D2
#define echoPin D1
#define cam D5
#define Button D7
DHT dht(DHTPIN, DHTTYPE);
Servo servo;
Servo servo1;

 unsigned long tt=0;
int counter;
// defines variables
long duration;
int distance;
// Your WiFi credentials.
// Set password to "" for open networks.
char ssid[] = "NSA";
char pass[] = "Orange11";
BlynkTimer timer;
// This function is called every time the Virtual Pin 0 state changes
BLYNK_WRITE(V0)
{
  // Set incoming value from pin V0 to a variable
  int value = param.asInt();

  Blynk.virtualWrite(V1, value);

}
// This function is called every time the device is connected to the Blynk.Cloud
BLYNK_CONNECTED()
{
  // Change Web Link Button message to "Congratulations!"
  Blynk.setProperty(V3, "offImageUrl", "https://static-image.nyc3.cdn.digitaloceanspaces.com/general/fte/congratulations.png");
  Blynk.setProperty(V3, "onImageUrl",  "https://static-image.nyc3.cdn.digitaloceanspaces.com/general/fte/congratulations_pressed.png");
  Blynk.setProperty(V3, "url", "https://docs.blynk.io/en/getting-started/what-do-i-need-to-blynk/how-quickstart-device-was-made");
}
// This function sends Arduino's uptime every second to Virtual Pin 2.
void myTimerEvent()
{
   float h = dht.readHumidity();
  // Read temperature as Celsius (the default)
  float t = dht.readTemperature();
  // Read temperature as Fahrenheit (isFahrenheit = true)
  float f = dht.readTemperature(true);
  // Check if any reads failed and exit early (to try again).
  if (isnan(h) || isnan(t) || isnan(f)) {
    Serial.println(F("Failed to read from DHT sensor!"));
    return;
  }
  // Compute heat index in Fahrenheit (the default)
  float hif = dht.computeHeatIndex(f, h);
  // Compute heat index in Celsius (isFahreheit = false)
  float hic = dht.computeHeatIndex(t, h, false);
  Serial.print(F("%  Temperature: "));
  Serial.println(t);
  Blynk.virtualWrite(V2, millis() / 1000);
      Blynk.virtualWrite(V4, t);
        Blynk.virtualWrite(V5, h);
        if(t>=26){
          digitalWrite(D0,0);
           Blynk.logEvent("high_temperature_");
          }
          else{ digitalWrite(D0,1);}
}
void setup() {
 Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);
  timer.setInterval(1000L, myTimerEvent);
 servo.attach(2);
  servo1.attach(12);
  Serial.println(F("DHTxx test!"));
  dht.begin();
 pinMode(trigPin, OUTPUT); // Sets the trigPin as an Output
   pinMode(D8, OUTPUT);
      pinMode(D1, OUTPUT);
  pinMode(echoPin, INPUT);  
  pinMode(Button, INPUT_PULLUP);
   pinMode(cam, INPUT); // Sets the echoPin as an Input
  Serial.begin(9600); // Starts the serial communication
}
void loop() {
    Blynk.run();
  timer.run();
  // Clears the trigPin
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  // Sets the trigPin on HIGH state for 10 micro seconds
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  // Reads the echoPin, returns the sound wave travel time in microseconds
  duration = pulseIn(echoPin, HIGH);
  // Calculating the distance
  distance = duration * 0.034 / 2;
  // Prints the distance on the Serial Monitor
  Serial.print("Distance: ");
  Serial.print(distance);
    Serial.print("-----");
  // Reading temperature or humidity takes about 250 milliseconds!
  // Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)

  Serial.println( digitalRead(Button));


      if(digitalRead(cam)==1 ){
 servo.write(180);
 Blynk.logEvent("authorized_accesss");
    } else{ servo.write(0);
}

    if( distance >2 && distance <10 && digitalRead(cam)==0 ){  
  counter++;
    Serial.print("-----");
  Serial.println(counter);}
if(counter>75){
   Blynk.logEvent("unkown_person");
  tt=millis();
  servo1.write(180);
    tone(D8,1000);
    delay(100);
     tone(D8,2000);
    delay(100);
tone(D8,3000);  
delay(200);
  if(digitalRead(Button)==1){
       noTone(D8);
 servo1.write(0);
   counter=0;
    }}

}
