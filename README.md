#include <Keypad.h>

const int ledPin = 13; 
int wrongAttempts = 0;
const int maxAttempts = 3;

// rows and columns for keypad
const byte ROWS = 4; 
const byte COLS = 4; 
char keys[ROWS][COLS] = {
  {'1','2','3','A'},
  {'4','5','6','B'},
  {'7','8','9','C'},
  {'*','0','#','D'}
};
byte rowPins[ROWS] = {9, 8, 7, 6}; 
byte colPins[COLS] = {5, 4, 3, 2}; 

Keypad keypad = Keypad( makeKeymap(keys), rowPins, colPins, ROWS, COLS );

char correctPassword[4] = {'2', '4', '0', '3'}; 
char enteredPassword[4];
int currentEntryIndex = 0;

void setup(){
  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, LOW);
  Serial.begin(9600);
}

void loop(){
  char key = keypad.getKey();

  if (key){
    Serial.println(key);

    enteredPassword[currentEntryIndex] = key;
    currentEntryIndex++;

    // When 4 digits are entered, check the password
    if (currentEntryIndex == 4){
      checkPassword();
      currentEntryIndex = 0; // Reset entry index for the next input
    }
  }
}

void checkPassword() {
  bool isCorrect = true;

  for (int i = 0; i < 4; i++){
    if (enteredPassword[i] != correctPassword[i]){
      isCorrect = false;
      break;
    }
  }

  if (isCorrect) {
    Serial.println("Password Correct!");
    wrongAttempts = 0; // Reset wrong attempts
  } else {
    wrongAttempts++;
    Serial.println("Incorrect Password!");

    if (wrongAttempts >= maxAttempts) {
      lightUpLED(); // Light up the LED after 3 wrong attempts
    }
  }
}

void lightUpLED() {
  Serial.println("Too many incorrect attempts. Lighting up the LED.");
  digitalWrite(ledPin, HIGH); // Turn on LED
  delay(5000);
  digitalWrite(ledPin, LOW);
}
