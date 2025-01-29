# boopdracht

#include <LiquidCrystal.h>

// Initialiseer LCD: RS, E, D4, D5, D6, D7
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

//int currentStep = 0, pStep = 0;
//int stepperPins[4] = { 8, 9, 10, 13 }; // Aangepaste pins voor de motor

long started_at;

void setup() {
  Serial.begin(9600);

  lcd.begin(16, 2); // Start LCD in 16x2-modus

  lcd.setCursor(0, 0);
  lcd.print("Munten Teller");

  //pinMode(8, OUTPUT);
  //pinMode(9, OUTPUT);
  //pinMode(10, OUTPUT);
  //pinMode(13, OUTPUT);

  started_at = millis();
}

// Teller en sensorstatus
int counter = 0;
int p_counter = -1;

bool sensorState_10_cent = false;
bool sensorState_20_cent = false;
bool sensorState_50_cent = false;

// Drempelwaarde voor sensoractivering
int sensorThreashHold = 75;

void loop() {
  // Lees de waarden van de sensoren
  int sensorValue_10_cent = analogRead(A0);
  int sensorValue_20_cent = analogRead(A1);
  int sensorValue_50_cent = analogRead(A2);

  Serial.print("Sensorwaarden: ");
  Serial.print(sensorValue_10_cent);
  Serial.print(", ");
  Serial.print(sensorValue_20_cent);
  Serial.print(", ");
  Serial.println(sensorValue_50_cent);

  // Controleer sensorwaarden en pas de teller aan
  if (sensorValue_10_cent < sensorThreashHold && sensorState_10_cent == false) counter += 10;
  if (sensorValue_20_cent < sensorThreashHold && sensorState_20_cent == false) counter += 20;
  if (sensorValue_50_cent < sensorThreashHold && sensorState_50_cent == false) counter += 50;

  sensorState_10_cent = sensorValue_10_cent < sensorThreashHold;
  sensorState_20_cent = sensorValue_20_cent < sensorThreashHold;
  sensorState_50_cent = sensorValue_50_cent < sensorThreashHold;

  // Als er een wijziging is in het totaal, update het LCD
  if (counter != p_counter) {
    printTotal();
    p_counter = counter;
  }

  // Stuur stappenmotor aan
  /*for (int a = 0; a < 10; a++) {
    for (int i = 0; i < 4; i++) {
      digitalWrite(stepperPins[i], (i == currentStep || i == pStep) ? HIGH : LOW);
      delay(1);
    }
    if (pStep != currentStep) pStep = currentStep;
    else currentStep = (currentStep + 1) % 4;
  }
  */
}

void printTotal() {
 
  lcd.setCursor(0, 1); // Ga naar de tweede rij van het LCD
  lcd.print("Totaal: " + String(((double)counter) / 100.0) + " EUR ");
}
