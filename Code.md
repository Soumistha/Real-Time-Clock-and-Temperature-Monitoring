# 📄 Full Code: Real-Time Clock and Temperature Monitoring

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h> // For controlling LCD display
#include "RTClib.h"            // For clock (DS1307)
#include "DHT.h"               // For temperature sensing (DHT22)

// Initialize LCD object
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Create RTC and DHT sensor objects
RTC_DS1307 rtc;                   // Connects to real-time clock
#define DHTP 12                   // DHT sensor data pin
#define DHTTYPE DHT22            // Specify DHT22 sensor type
DHT dht(DHTP, DHTTYPE);          // Create DHT object

#define LED_PIN 13               // LED connected to pin 13
#define TEMP_LIMIT 40.0         // Temperature threshold in Celsius

void setup() {
  lcd.begin(16, 2);              // Initialize LCD with 16 cols and 2 rows
  lcd.backlight();              // Turn on LCD backlight
  dht.begin();                  // Start DHT sensor
  pinMode(LED_PIN, OUTPUT);     // Set LED pin as output

  // Check if RTC is connected properly
  if (!rtc.begin()) {
    lcd.print("RTC ERR");
    while (1);                 
  }

  // if (!rtc.isrunning()) {
  //   rtc.adjust(DateTime(F(__DATE__), F(__TIME__)));
  // }
}

void loop() {
  DateTime current = rtc.now();          // Get current time from RTC
  float temp = dht.readTemperature();    // Get temperature from sensor

  // Display time on LCD
  lcd.setCursor(0, 0);
  lcd.print("Time ");
  if (current.hour() < 10) lcd.print("0");
  lcd.print(current.hour());
  lcd.print(":");
  if (current.minute() < 10) lcd.print("0");
  lcd.print(current.minute());
  lcd.print(":");
  if (current.second() < 10) lcd.print("0");
  lcd.print(current.second());

  // Display temperature on LCD
  lcd.setCursor(0, 1);
  lcd.print("Temp ");
  lcd.print(temp, 1);
  lcd.print((char)223);   // Print degree symbol
  lcd.print("C   ");

  // If temperature exceeds threshold, turn on LED
  digitalWrite(LED_PIN, temp > TEMP_LIMIT);

  delay(1000); // Wait for 1 second
}
