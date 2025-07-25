#define BLYNK_TEMPLATE_ID "TMPL4qPz2l0Ft"
#define BLYNK_TEMPLATE_NAME "Baby"
#define BLYNK_AUTH_TOKEN "i0tk0tNxlN1yybjFk_USiSNacCb8IJFE"

#include <WiFi.h>
#include <WiFiClient.h>
#include <BlynkSimpleEsp32.h>
#include <ESP32Servo.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

// OLED config
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

// WiFi Credentials
char ssid[] = "iPhone";
char pass[] = "meesmanoj";

// Pin Definitions
#define SOUND_SENSOR_PIN 34
#define PIR_SENSOR_PIN 35
#define RAIN_SENSOR_PIN 32
#define BUZZER_PIN  27
#define PUSH_BUTTON_PIN 14
#define SERVO_PIN  26

Servo cradleServo;
bool isShaking = false;
BlynkTimer timer;

// Cradle movement (slow to-and-fro)
void shakeCradle() {
  if (!isShaking) {
    isShaking = true;
    Blynk.virtualWrite(V4, 255); // Cradle ON indicator

    for (int pos = 0; pos <= 60; pos++) {
      cradleServo.write(pos);
      delay(15);
    }
    for (int pos = 60; pos >= 0; pos--) {
      cradleServo.write(pos);
      delay(15);
    }

    Blynk.virtualWrite(V4, 0); // Cradle OFF indicator
    isShaking = false;
  }
}

// OLED status display
void updateOLED(String sound, String motion, String towel) {
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 0);
  display.println("Baby Cradle Status:");
  display.print("Sound: ");
  display.println(sound);
  display.print("Motion: ");
  display.println(motion);
  display.print("Baby Towel: ");
  display.println(towel);
  display.display();
}

// Sensor check
void checkSensors() {
  int soundVal = digitalRead(SOUND_SENSOR_PIN);
  int motionVal = digitalRead(PIR_SENSOR_PIN);
  int rainVal = digitalRead(RAIN_SENSOR_PIN);
  int buttonVal = digitalRead(PUSH_BUTTON_PIN);

  String soundStatus = "No";
  String motionStatus = "No";
  String towelStatus = "Dry";

  if (soundVal == HIGH) {
    Blynk.virtualWrite(V0, 255);
    soundStatus = "Yes";
    shakeCradle();
    digitalWrite(BUZZER_PIN, HIGH);
    delay(1000);
    digitalWrite(BUZZER_PIN, LOW);
  } else {
    Blynk.virtualWrite(V0, 0);
  }

  if (motionVal == HIGH) {
    Blynk.virtualWrite(V1, 255);
    motionStatus = "Yes";
    shakeCradle();
    digitalWrite(BUZZER_PIN, HIGH);
    delay(1000);
    digitalWrite(BUZZER_PIN, LOW);
  } else {
    Blynk.virtualWrite(V1, 0);
  }

  if (rainVal == LOW) {
    Blynk.virtualWrite(V2, 255);
    towelStatus = "Wet";
  } else {
    Blynk.virtualWrite(V2, 0);
  }

  if (buttonVal == LOW) {
    shakeCradle();
  }

  updateOLED(soundStatus, motionStatus, towelStatus);
}

// Manual cradle from Blynk button V3
BLYNK_WRITE(V3) {
  int value = param.asInt();
  if (value == 1) {
    shakeCradle();
  }
}

void setup() {
  Serial.begin(115200);

  // OLED Initialization
  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("OLED init failed"));
    while (true); // Stop here if OLED fails
  }

  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(10, 25);
  display.println("System Installing...");
  display.display();
  delay(2000);

  Blynk.begin(BLYNK_AUTH_TOKEN, ssid, pass);

  display.clearDisplay();
  display.setCursor(10, 25);
  display.println("WiFi Connected!");
  display.display();
  delay(2000);

  pinMode(SOUND_SENSOR_PIN, INPUT);
  pinMode(PIR_SENSOR_PIN, INPUT);
  pinMode(RAIN_SENSOR_PIN, INPUT);
  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(PUSH_BUTTON_PIN, INPUT_PULLUP);

  cradleServo.attach(SERVO_PIN);
  cradleServo.write(0);

  timer.setInterval(1000L, checkSensors);
}

void loop() {
  Blynk.run();
  timer.run();
}
