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
