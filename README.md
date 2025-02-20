# Lora-
#include <SPI.h>
#include <LoRa.h>
#include <DHT.h> // Include DHT library
#define SS D8 // LoRa module pins
#define RST D3
#define DI0 D2
#define BAND 433E6 // LoRa frequency band
#define ENCRYPT 0x78 // LoRa encryption sync word
#define DHTPIN D4 // DHT11 pin
#define DHTTYPE DHT11
#define MQ_PIN A0 // MQ135 analog pin
#define UV_PIN 10 // UV light sensor analog pin
#define TX_P 17 // LoRa TX power (example value, adjust as needed)
const int soilMoisturePin = D0; // Soil moisture sensor digital output connected to D0
const int ledPin = D1; // LED connected to D1
const int motorIn1 = D2; // Motor IN1 connected to D2
const int motorIn2 = D3; // Motor IN2 connected to D3
DHT dht(DHTPIN, DHTTYPE); // Initialize DHT object
void setup() {
Serial.begin(9600);
while (!Serial);
Serial.println("LoRa Sender");
// Initialize LoRa module
LoRa.setPins(SS, RST, DI0); 
DHT dht(DHTPIN, DHTTYPE); // Initialize DHT object
void setup() {
Serial.begin(9600);
while (!Serial);
Serial.println("LoRa Sender");
// Initialize LoRa module
LoRa.setPins(SS, RST, DI0);
if (!LoRa.begin(BAND)) {
Serial.println("Starting LoRa failed!");
while (1);
}
LoRa.setTxPower(TX_P); // Set LoRa TX power
LoRa.setSyncWord(ENCRYPT);
// Initialize DHT11 sensor
dht.begin();
// Setup for soil moisture sensor, LED, and motor
pinMode(soilMoisturePin, INPUT);
pinMode(ledPin, OUTPUT);
digitalWrite(ledPin, LOW);
pinMode(motorIn1, OUTPUT);
pinMode(motorIn2, OUTPUT);
digitalWrite(motorIn1, LOW);
digitalWrite(motorIn2, LOW);
}
void loop() {
// Read DHT11 sensor data
float humidity = dht.readHumidity();
float temperature = dht.readTemperature();
// Read MQ135 sensor data
int mqValue = analogRead(MQ_PIN);
// Read soil moisture sensor data
int soilMoistureValue = digitalRead(soilMoisturePin);
// Read UV light sensor data
int uvValue = analogRead(UV_PIN);
// Prepare LoRa packet
String data = "Humidity: " + String(humidity) + "%, Temperature: " +
String(temperature) + "C, MQ135: " + String(mqValue) + ", Soil Moisture: " +
String(soilMoistureValue) + ", UV Light: " + String(uvValue);
// Send packet via LoRa
LoRa.beginPacket();
LoRa.print(data);
LoRa.endPacket();
Serial.println("Packet sent: " + data);
// Control LED and motor based on soil moisture value
if (soilMoistureValue == LOW) {
digitalWrite(ledPin, HIGH); // Turn ON the LED
digitalWrite(motorIn1, HIGH); // Turn ON the motor (clockwise direction)
digitalWrite(motorIn2, LOW);
} else {
digitalWrite(ledPin, LOW); // Turn OFF the LED
digitalWrite(motorIn1, LOW); // Turn OFF the motor
digitalWrite(motorIn2, LOW);
}
delay(100); // Send data every 10 seconds
