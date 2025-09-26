# Bus-Identification-System-Blind-RFID
An RFID-based bus identification system to assist visually impaired individuals by providing voice alerts for bus recognition.
[MiniBatchB10-PublishedPaper.pdf](https://github.com/user-attachments/files/22554219/MiniBatchB10-PublishedPaper.pdf)
[MiniBatchB10-code.txt](https://github.com/user-attachments/files/22554232/MiniBatchB10-code.txt)

#include <SPI.h>
#include <MFRC522.h>

// Define pin connections
#define SS_PIN 10 // RFID module SS pin
#define RST_PIN 9 // RFID module RST pin

// Initialize RFID reader
MFRC522 rfid(SS_PIN, RST_PIN);

// Voice module pins
#define VOICE_RX 2
#define VOICE_TX 3

// Create object for voice module
SoftwareSerial voiceSerial(VOICE_RX, VOICE_TX);

// Tag ID for Bus 1 and Bus 2 (Replace with your own tag IDs)
byte bus1Tag[] = {4B00C6EA9DFA}; // Example tag for bus 1
byte bus2Tag[] = {4B00C6D795CF}; // Example tag for bus 2

void setup() {
  // Start serial communication
  Serial.begin(9600);
  
  // Initialize RFID reader
  SPI.begin();
  rfid.PCD_Init();
  
  // Initialize voice serial communication
  voiceSerial.begin(9600);

  Serial.println("Bus Identification System for Blind");
}

void loop() {
  // Check if a tag is detected
  if (rfid.PICC_IsNewCardPresent()) {
    if (rfid.PICC_ReadCardSerial()) {
      // Print tag UID to serial monitor
      Serial.print("Tag UID: ");
      for (byte i = 0; i < rfid.uid.size; i++) {
        Serial.print(rfid.uid.uidByte[i], HEX);
        Serial.print(" ");
      }
      Serial.println();

      // Compare the scanned tag with predefined tags and trigger voice message
      if (compareTag(rfid.uid.uidByte, bus1Tag)) {
        Serial.println("Bus 1 detected!");
        voiceSerial.println("bus1");
      }
      else if (compareTag(rfid.uid.uidByte, bus2Tag)) {
        Serial.println("Bus 2 detected!");
        voiceSerial.println("bus2");
      }
      else {
        Serial.println("Unknown Bus!");
        voiceSerial.println("unknown");
      }

      // Halt RFID reader after processing the tag
      rfid.PICC_HaltA();
      rfid.PCD_StopCrypto1();
    }
  }
}

// Function to compare RFID tag with predefined tag
bool compareTag(byte *tag1, byte *tag2) {
  for (byte i = 0; i < 4; i++) {
    if (tag1[i] != tag2[i]) {
      return false;
    }
  }
  return true;
}
Expl
