# Accident-Alert-System

#include <SoftwareSerial.h>
#include <TinyGPS++.h>
static const int RXPin = 2, TXPin = 3;
static const uint32_t GPSBaud = 9600;
                                                                                                                                                                                                                              int  m = 9740;
 
                                                                                                                                                                                                                       int y = 71;
// The TinyGPS++ object
TinyGPSPlus gps;

// The serial connection to the GPS device
SoftwareSerial ss(RXPin, TXPin); // for gps

SoftwareSerial SIM900(7, 8); // for gsm module

int sensor = A1; // Vibration sensor connected here
int led = 13; // Vibration sensor output indication

String textForSMS;
 int limits = 4; // Vibration Sensor
 
 String datareal; 
 String dataimaginary;
 String combined;
 int raw = 1000000;
 
 String datareal2;
 String dataimaginary2;
 String combined2;
 
 
 double longitude; 
 double latitude;
void setup()
{
  SIM900.begin(19200);
  Serial.begin(9600);
    ss.begin(GPSBaud);
  delay(10000);  // give time to log on to network. 
  Serial.println(" logging time completed!");
  randomSeed(analogRead(0));
  pinMode(limits, INPUT);
  digitalWrite(limits, HIGH);
  
    pinMode(sensor, INPUT);
  pinMode(led, OUTPUT);
  digitalWrite(led, LOW);
  
    Serial.println(F("DeviceExample.ino"));
  Serial.println(F("A simple demonstration of TinyGPS++ with an attached GPS module"));
  Serial.print(F("Testing TinyGPS++ library v. ")); 
  Serial.println(TinyGPSPlus::libraryVersion());

  Serial.println();
}
 

 
void sendSMS(String message)
{
  SIM900.print("AT+CMGF=1\r");                     // AT command to send SMS message
  delay(100);
 SIM900.println("AT + CMGS = \"+919066519997\"");  // recipient's mobile number, in international format
  delay(100);
  SIM900.println(message);                         // message to send
  delay(100);
  SIM900.println((char)26);                        // End AT command with a ^Z, ASCII code 26
  delay(100); 
  SIM900.println();
  delay(5000);                                     // give module time to send SMS
                                  
}
 
void loop()
{
  int reading; 
    reading = analogRead(sensor);
// Serial.println(reading);
//  delay(1000);
  
 // for the Vibration sensor
//*****************************************************************
//****************************************************************
if(reading > 800)
{
   digitalWrite(led, HIGH);
   
 displayInfo();
 

latitude = gps.location.lat(), 6 ;
longitude = gps.location.lng(), 6 ;
// for latitude
long datareal = int(latitude);
int fahad = ( latitude - datareal) * 100000;

// for longitude
long datareal2 = int(longitude);
int fahad2 = (longitude - datareal2 ) * 100000;

  textForSMS = "Longitude:  ";
  textForSMS.concat(datareal2);
  textForSMS = textForSMS + ".";
  textForSMS.concat(fahad2);
  textForSMS = textForSMS + " Latitude: ";
   textForSMS.concat(datareal);
    textForSMS = textForSMS + ".";
  textForSMS.concat(fahad);
 textForSMS = textForSMS + " Vehicle meet with an accident.";  
  sendSMS(textForSMS);
  Serial.println(textForSMS);
  Serial.println("message sent.");
  delay(5000);
 
}
 
    // This sketch displays information every time a new sentence is correctly encoded.
  while (ss.available() > 0)
    if (gps.encode(ss.read()))
      displayInfo();

  if (millis() > 5000 && gps.charsProcessed() < 10)
  {
    Serial.println(F("No GPS detected: check wiring."));
    while(true);
  }
 // for the button 
//*****************************************************************
//****************************************************************
  if(digitalRead(limits) == High)
  {
    displayInfo();
latitude = gps.location.lat(), 6 ;
longitude = gps.location.lng(), 6 ;
// for latitude
long datareal = int(latitude);
int fahad = ( latitude - datareal) * 100000;

// for longitude
long datareal2 = int(longitude);
int fahad2 = (longitude - datareal2 ) * 100000;

  textForSMS = "Longitude:  ";
  textForSMS.concat(datareal2);
  textForSMS = textForSMS + ".";
  textForSMS.concat(fahad2);
  textForSMS = textForSMS + " Latitude: ";
   textForSMS.concat(datareal);
    textForSMS = textForSMS + ".";
  textForSMS.concat(fahad);
 textForSMS = textForSMS + "  Vehicle meet with an accident.";  
  sendSMS(textForSMS);
  Serial.println(textForSMS);
  Serial.println("message sent.");
  delay(5000);
  }
  else
  digitalWrite(limits, HIGH);
   digitalWrite(led, LOW);
}


void displayInfo()
{
  Serial.print(F("Location: ")); 
  if (gps.location.isValid())
  {
    Serial.print(gps.location.lat(), 6);
    Serial.print(F(","));
    Serial.print(gps.location.lng(), 6);
    Serial.print(" ");
    Serial.print(F("Speed:"));
    Serial.print(gps.speed.kmph());
  }
  else
  {
    Serial.print(F("INVALID"));
  }

  Serial.print(F("  Date/Time: "));
  if (gps.date.isValid())
  {
    Serial.print(gps.date.month());
    Serial.print(F("/"));
    Serial.print(gps.date.day());
    Serial.print(F("/"));
    Serial.print(gps.date.year());
  }
  else
  {
    Serial.print(F("INVALID"));
  }

  Serial.print(F(" "));
  if (gps.time.isValid())
  {
    if (gps.time.hour() < 10) Serial.print(F("0"));
    Serial.print(gps.time.hour());
    Serial.print(F(":"));
    if (gps.time.minute() < 10) Serial.print(F("0"));
    Serial.print(gps.time.minute());
    Serial.print(F(":"));
    if (gps.time.second() < 10) Serial.print(F("0"));
    Serial.print(gps.time.second());
    Serial.print(F("."));
    if (gps.time.centisecond() < 10) Serial.print(F("0"));
    Serial.print(gps.time.centisecond());
  }
  else
  {
    Serial.print(F("INVALID"));
  }

  Serial.println();
}
