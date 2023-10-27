# Project-Embedded
## วัดความอุณหภูมิและความชื้นจาก DHT11  

```css
 #include "DHT.h"

#define DHTPIN 2       // กำหนดขาที่เชื่อมกับ DHT11
#define DHTTYPE DHT11  // เลือกประเภทของ DHT (DHT11, DHT22, DHT21)

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  dht.begin();
}

void loop() {
  delay(2000);  // รอ 2 วินาที

  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  if (isnan(humidity) || isnan(temperature)) {
    Serial.println("ล้มเหลวในการอ่านค่าจาก DHT11");
  } else {
    Serial.print("ความชื้น: ");
    Serial.print(humidity);
    Serial.print("%\t");
    Serial.print("อุณหภูมิ: ");
    Serial.print(temperature);
    Serial.println("°C");
  }
}
```
## เพิ่มความชื้นในดิน และ led แสดงสถานะ ความชื้นในดิน 

```css
#include "DHT.h"

#define DHTPIN 19       // กำหนดขาที่เชื่อมกับ DHT11 18/19/21
#define DHTTYPE DHT11  // เลือกประเภทของ DHT (DHT11, DHT22, DHT21)
#define SOIL_MOISTURE_PIN 33
#define LED_PIN 32

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  dht.begin();
  pinMode(SOIL_MOISTURE_PIN, INPUT);
  pinMode(LED_PIN, OUTPUT);
}

void loop() {
  delay(2000);  // รอ 2 วินาที

  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  if (isnan(humidity) || isnan(temperature)) {
    Serial.println("ล้มเหลวในการอ่านค่าจาก DHT11");
  } else {
    Serial.print("ความชื้น: ");
    Serial.print(humidity);
    Serial.print("%\t");
    Serial.print("อุณหภูมิ: ");
    Serial.print(temperature);
    Serial.println("°C");
  }

  int soilMoisture = analogRead(SOIL_MOISTURE_PIN);

  // ตรวจสอบค่าความชื้นในดินและควบคุม LED
  if (soilMoisture < 500) { // ความชื้นในดินต่ำ
    digitalWrite(LED_PIN, HIGH); // เปิด LED
  } else {
    digitalWrite(LED_PIN, LOW); // ปิด LED
  }
}
```
## เปิดปั๊มน้ำที่เชื่อมกับ relay

```css
#include "DHT.h"

#define DHTPIN 19       // กำหนดขาที่เชื่อมกับ DHT11 18/19/21
#define DHTTYPE DHT11  // เลือกประเภทของ DHT (DHT11, DHT22, DHT21)
#define SOIL_MOISTURE_PIN 32
#define LED_PIN 33
#define RELAY_PIN 27

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  dht.begin();
  pinMode(SOIL_MOISTURE_PIN, INPUT);
  pinMode(LED_PIN, OUTPUT);
  pinMode(RELAY_PIN, OUTPUT);
  digitalWrite(RELAY_PIN, LOW); // ปิดรีเลย์เริ่มต้น
}

void loop() {
  delay(2000);  // รอ 2 วินาที

  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  if (isnan(humidity) || isnan(temperature)) {
    Serial.println("ล้มเหลวในการอ่านค่าจาก DHT11");
  } else {
    Serial.print("ความชื้น: ");
    Serial.print(humidity);
    Serial.print("%\t");
    Serial.print("อุณหภูมิ: ");
    Serial.print(temperature);
    Serial.println("°C");
  }

  int soilMoisture = analogRead(SOIL_MOISTURE_PIN);

  // ตรวจสอบค่าความชื้นในดินและควบคุม LED
  if (soilMoisture < 1500) { // ความชื้นในดินต่ำกว่า 1500
    digitalWrite(LED_PIN, HIGH); // เปิด LED
    digitalWrite(RELAY_PIN, HIGH); // เปิดรีเลย์ (เปิดปั๊มน้ำ)
    //Serial.println("ความชื้นในดิน: แห้ง");
  } else {
    digitalWrite(LED_PIN, LOW); // ปิด LED
    digitalWrite(RELAY_PIN, LOW); // ปิดรีเลย์ (ปิดปั๊มน้ำ)
    //Serial.println("ความชื้นในดิน: ชื้น");
  }

  // แสดงค่าความชื้นในดินบน Serial Monitor
  Serial.print("ความชื้นในดิน: ");
  Serial.print(soilMoisture);
  Serial.println();
}
```

## Firbase 

```css
#include <WiFi.h>
#include <FirebaseESP32.h>

#define WIFI_SSID "Areena’s iphone"
#define WIFI_PASSWORD "Treasure12"
#define FIREBASE_HOST "projectsmartfarm-9f755-default-rtdb.firebaseio.com"
#define FIREBASE_AUTH "gXr5az2fZPSqR2tb3xrySWLsWQGALTIXgUCax4Rh"

#define RELAY_PIN 27 // ขาเชื่อมรีเลย์
#define SOIL_MOISTURE_SENSOR_PIN 34 // ขาเชื่อมเซนเซอร์ความชื้นในดิน
#define TEMPERATURE_SENSOR_PIN 32 // ขาเชื่อมเซนเซอร์อุณหภูมิ

FirebaseData fbdo;
FirebaseConfig config; // ประกาศ FirebaseConfig
FirebaseAuth auth; // ประกาศ FirebaseAuth

void setup() 

{
  pinMode(RELAY_PIN, OUTPUT);
  digitalWrite(RELAY_PIN, LOW); // ปิดรีเลย์เริ่มต้น

  Serial.begin(115200);
  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);

  while (WiFi.status() != WL_CONNECTED) 
  {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");

  Firebase.begin(&config, &auth);
  Firebase.reconnectWiFi(true);
  Serial.println("Connected to Firebase");
}

void loop() 
{
  // อ่านค่าความชื้นในดิน
  int soilMoisture = analogRead(SOIL_MOISTURE_SENSOR_PIN);

  // อ่านค่าอุณหภูมิ
  int temperature = analogRead(TEMPERATURE_SENSOR_PIN);
  float voltage = temperature * 5.0 / 1024.0;
  float degreesC = (voltage - 0.5) * 100.0;

  // ส่งข้อมูลความชื้นในดินและอุณหภูมิขึ้น Firebase Realtime Database
  String soilMoisturePath = "/sensorData/soilMoisture";
  String temperaturePath = "/sensorData/temperature";
  if (Firebase.setInt(fbdo, soilMoisturePath, soilMoisture)) 
  {
    Serial.println("Soil Moisture sent to Firebase");
  } 
  else 
  {
    Serial.println("Failed to send Soil Moisture to Firebase");
  }

  if (Firebase.setFloat(fbdo, temperaturePath, degreesC)) 
  {
    Serial.println("Temperature sent to Firebase");
  } 
  else 
  {
    Serial.println("Failed to send Temperature to Firebase");
  }

  // ตรวจสอบความชื้นในดินและควบคุมปั๊มน้ำ
  if (soilMoisture < 500) 
  {
    digitalWrite(RELAY_PIN, HIGH); // เปิดปั๊มน้ำ
  } 
  else 
  {
    digitalWrite(RELAY_PIN, LOW); // ปิดปั๊มน้ำ
  }
  
  // อ่านค่าจาก Firebase
  String pumpStatusPath = "/controlData/pumpStatus";
  String temperatureReadPath = "/sensorData/temperature";
  String soilMoistureReadPath = "/sensorData/soilMoisture"; // เพิ่ม Path สำหรับอ่านความชื้นในดิน
  
  String pumpStatus;
  float temperatureRead;
  int soilMoistureRead; // เพิ่มตัวแปรสำหรับอ่านความชื้นในดิน
  
  if (Firebase.getString(fbdo, pumpStatusPath)) 
  {
    pumpStatus = fbdo.stringData();
    Serial.print("Pump Status: ");
    Serial.println(pumpStatus);
  } 
  else 
  {
    Serial.println("Failed to read Pump Status from Firebase");
  }
  
  if (Firebase.getFloat(fbdo, temperatureReadPath)) 
  {
    temperatureRead = fbdo.floatData();
    Serial.print("Temperature Read: ");
    Serial.println(temperatureRead);
  } 
  else 
  {
    Serial.println("Failed to read Temperature Read from Firebase");
  }
  
  // เพิ่มการอ่านความชื้นในดินจาก Firebase
  if (Firebase.getInt(fbdo, soilMoistureReadPath)) 
  {
    soilMoistureRead = fbdo.intData();
    Serial.print("Soil Moisture Read: ");
    Serial.println(soilMoistureRead);
  } 
  else 
  {
    Serial.println("Failed to read Soil Moisture Read from Firebase");
  }

  delay(30000); // รอ 5 วินาที
}

```
## Final

```css
#include "DHT.h"

#define DHTPIN 19       // กำหนดขาที่เชื่อมกับ DHT11 18/19/21
#define DHTTYPE DHT11  // เลือกประเภทของ DHT (DHT11, DHT22, DHT21)
#define SOIL_MOISTURE_PIN 32
#define LED_PIN 21
//#define RELAY_PIN 27

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(921600);
  dht.begin();
  pinMode(SOIL_MOISTURE_PIN, INPUT);
  pinMode(LED_PIN, OUTPUT);
  //pinMode(RELAY_PIN, OUTPUT);
  //digitalWrite(RELAY_PIN, LOW); // ปิดรีเลย์เริ่มต้น
}

void loop() {
  delay(2000);  // รอ 2 วินาที

  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  int soilMoisture = analogRead(SOIL_MOISTURE_PIN);

   
  if (soilMoisture < 2000) { // ความชื้นในดินต่ำกว่า 2200
    digitalWrite(LED_PIN, LOW); // เปิด LED
    //digitalWrite(RELAY_PIN , LOW); // เปิดรีเลย์ (เปิดปั๊มน้ำ)
    //Serial.println("ความชื้นในดิน: แห้ง");
  } else {
    digitalWrite(LED_PIN, HIGH); // ปิด LED
    //digitalWrite(RELAY_PIN , LOW); // ปิดรีเลย์ (ปิดปั๊มน้ำ)
    //Serial.println("ความชื้นในดิน: ชื้น");
  }
  if (isnan(humidity) || isnan(temperature)) {
    Serial.println("ล้มเหลวในการอ่านค่าจาก DHT11");
  } else {
    Serial.print("ความชื้น: ");
    Serial.print(humidity);
    Serial.print("%\t");
    Serial.print("อุณหภูมิ: ");
    Serial.print(temperature);
    Serial.println("°C");
  }
    Serial.print("ความชื้นในดิน: ");
    Serial.print(soilMoisture);
    Serial.println();

}
```
