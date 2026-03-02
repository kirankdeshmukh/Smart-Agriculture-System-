#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <DHT.h>

// LCD and DHT setup
LiquidCrystal_I2C lcd(0x27, 16, 2);  // LCD address 0x27, 16 columns, 2 rows
#define DHTPIN 4                     // Pin connected to DHT sensor
#define DHTTYPE DHT11                // DHT 11 sensor type
DHT dht(DHTPIN, DHTTYPE);

#define SOIL_MOISTURE_PIN 5          // Digital pin for soil moisture sensor
#define PUMP_PIN 2                   // Digital pin connected to the pump

void setup() {
  // Initialize LCD
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print("Initializing...");

  // Initialize DHT sensor
  dht.begin();

  // Initialize pump control pin
  pinMode(PUMP_PIN, OUTPUT);
  digitalWrite(PUMP_PIN, LOW); // Ensure pump is off initially

  // Initialize soil moisture pin
  pinMode(SOIL_MOISTURE_PIN, INPUT);

  delay(2000); // Wait for initialization
  lcd.clear();
}

void loop() {
  // Read humidity and temperature
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  // Display humidity and temperature
  lcd.clear();
  if (isnan(humidity) || isnan(temperature)) {
    lcd.setCursor(0, 0);
    lcd.print("Sensor Error!");
  } else {
    lcd.setCursor(0, 0);
    lcd.print("H: ");
    lcd.print(humidity, 1); // Display humidity with 1 decimal
    lcd.print("% ");

    lcd.setCursor(0, 1);
    lcd.print("T: ");
    lcd.print(temperature, 1); // Display temperature with 1 decimal
    lcd.print("C");
  }

  delay(3000); // Display for 3 seconds

  // Read soil moisture state (HIGH = Dry, LOW = Wet)
  int soilMoistureState = digitalRead(SOIL_MOISTURE_PIN);

  // Display soil moisture state and control pump
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Soil Status:");

  if (soilMoistureState == HIGH) {
    lcd.setCursor(0, 1);
    lcd.print("Dry - Pump ON");
    digitalWrite(PUMP_PIN, HIGH); // Turn pump on
  } else {
    lcd.setCursor(0, 1);
    lcd.print("Wet - Pump OFF");
    digitalWrite(PUMP_PIN, LOW); // Turn pump off
  }

  delay(3000); // Display for 3 seconds
}
