#include <Wire.h>
#include <Adafruit_LiquidCrystal.h>

// ================= LCD =================
Adafruit_LiquidCrystal lcd(32); // I2C address (decimal)

// ================= PIN =================
int trig = 9;
int echo = 10;

int ledHijau = 3;
int ledKuning = 5;
int ledMerah = 6;

int buzzer = 8;
int buttonPin = 2;

int potPin = A0;

// ================= VARIABEL =================
bool buzzerAktif = true;

// ================= INTERRUPT =================
void toggleBuzzer() {
  buzzerAktif = !buzzerAktif;
}

void setup() {
  Serial.begin(9600);

  // SENSOR
  pinMode(trig, OUTPUT);
  pinMode(echo, INPUT);

  // LED
  pinMode(ledHijau, OUTPUT);
  pinMode(ledKuning, OUTPUT);
  pinMode(ledMerah, OUTPUT);

  // BUZZER
  pinMode(buzzer, OUTPUT);

  // BUTTON (INTERRUPT)
  pinMode(buttonPin, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(buttonPin), toggleBuzzer, FALLING);

  // LCD
  lcd.begin(16, 2);

  lcd.setCursor(0, 0);
  lcd.print("System Ready");
  delay(1000);
  lcd.clear();
}

void loop() {
  long durasi;
  float jarak;

  // ===== SENSOR =====
  digitalWrite(trig, LOW);
  delayMicroseconds(2);

  digitalWrite(trig, HIGH);
  delayMicroseconds(10);
  digitalWrite(trig, LOW);

  durasi = pulseIn(echo, HIGH);
  jarak = durasi * 0.034 / 2;

  // ===== POTENSIOMETER =====
  int nilai = analogRead(potPin);
  int batas = map(nilai, 0, 1023, 10, 100);

  // ===== SERIAL =====
  Serial.print("Jarak: ");
  Serial.print(jarak);
  Serial.print(" cm | Batas: ");
  Serial.print(batas);
  Serial.print(" cm | Buzzer: ");
  Serial.println(buzzerAktif ? "ON" : "OFF");

  // ===== LED =====
  if (jarak > batas) {
    digitalWrite(ledHijau, HIGH);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledMerah, LOW);
  }
  else if (jarak > batas / 2) {
    digitalWrite(ledHijau, LOW);
    digitalWrite(ledKuning, HIGH);
    digitalWrite(ledMerah, LOW);
  }
  else {
    digitalWrite(ledHijau, LOW);
    digitalWrite(ledKuning, LOW);
    digitalWrite(ledMerah, HIGH);
  }

  // ===== BUZZER DINAMIS + INTERRUPT =====
  if (jarak < batas && buzzerAktif) {

    if (jarak < 5) {
      tone(buzzer, 1200); // bunyi terus
    } 
    else {
      int interval = map(jarak, 5, batas, 100, 800);
      interval = constrain(interval, 100, 800);

      tone(buzzer, 1000);
      delay(80);
      noTone(buzzer);
      delay(interval);
    }

  } else {
    noTone(buzzer);
  }

  // ===== LCD BARIS 1 =====
  lcd.setCursor(0, 0);
  lcd.print("J:");
  lcd.print(jarak);
  lcd.print("cm B:");
  lcd.print(batas);
  lcd.print("   ");

  // ===== LCD BARIS 2 (TENGAH) =====
  lcd.setCursor(0, 1);
  lcd.print("                ");

  if (jarak > batas) {
    lcd.setCursor(6, 1);
    lcd.print("AMAN");
  }
  else if (jarak > batas / 2) {
    lcd.setCursor(3, 1);
    lcd.print("HATI-HATI");
  }
  else {
    lcd.setCursor(6, 1);
    lcd.print("STOP");
  }

  delay(50);
}
