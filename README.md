# ESP32-CAM AI Face Detection and RFID Keypad Automatic Door Lock

## Project Overview
This project implements an **Artificial Intelligence Face Detection** system using the **ESP32-CAM** module that triggers an **automatic door lock**. Additionally, it supports **RFID tag-based** access control and **keypad password entry** for unlocking the door. The system uses a **relay** to control the door mechanism and **LED indicators** to provide feedback for access granted or denied.

## Features
- **Face Detection:** Unlock the door when the ESP32-CAM detects a known face.
- **RFID Access:** Use RFID tags for access control. Predefined RFID tags allow the door to unlock.
- **Keypad Password Entry:** Users can unlock the door by entering a password via the 4x4 keypad.
- **LED Indicators:** Green LED for access granted, Red LED for access denied.
- **LCD Display:** Displays messages like password input, access granted, or denied.

## Hardware Components
- **ESP32-CAM** (AI Thinker model)
- **RFID Reader (MFRC522)**
- **Relay Module** (to control door lock)
- **16x2 I2C LCD Display**
- **4x4 Keypad**
- **Red and Green LEDs**
- **RFID Tags**
- **Power Supply** (for ESP32-CAM and other components)

## Pin Connections
- **Relay:** GPIO 2 (controls door lock)
- **Red LED:** GPIO 13
- **Green LED:** GPIO 12
- **RFID Reader (MFRC522):**
  - SS_PIN: GPIO 10
  - RST_PIN: GPIO 5
- **Keypad:**
  - Row Pins: GPIO 16, 3, 4, 5
  - Column Pins: GPIO 6, 8, 7, 14
- **ESP32-CAM camera pins:** Defined in `camera_pins.h`

## Software Libraries
- **ESP32 Camera Library:** For initializing and capturing images from the ESP32-CAM.
- **WiFi Library:** To connect the ESP32-CAM to a WiFi network.
- **Keypad Library:** For handling input from the 4x4 keypad.
- **MFRC522 Library:** For handling RFID tag detection.
- **LiquidCrystal_I2C Library:** For interacting with the 16x2 LCD over I2C.

## WiFi Configuration
Set your WiFi credentials in the following section of the code:
```cpp
const char* ssid = "-----------"; // WiFi SSID
const char* password = "----------"; // WiFi Password
```

## RFID Tag Configuration

The RFID section of this project allows door access via registered RFID tags. Each RFID tag has a unique UID that is checked against pre-registered UIDs for authentication.

### Key Components:
- **MFRC522 RFID Reader**
- **SPI Communication Protocol**
- **Pre-registered RFID Tag UIDs**

### UID List and Corresponding Names:
In the code, the UID for each RFID tag is stored as a byte array. Each UID is mapped to a corresponding user name. The code looks like this:

```cpp
byte keyTagUID[][4] = {
    {0x15, 0xB2, 0x7A, 0x32},  // Alan's UID
    {0xF5, 0x1D, 0xD3, 0x5C},  // Ashwin Karthik's UID
    {0xC5, 0x7D, 0x61, 0x5B}   // Gowtham's UID
};

String name[] = {"Alan", "Ashwin Karthik", "Gowtham"};
```

These UIDs are checked against the scanned RFID tag to verify if access should be granted.

### How It Works:
1. When an RFID tag is brought near the MFRC522 reader, the system reads the UID from the card.
2. The system checks if the UID matches any of the pre-stored UIDs in the `keyTagUID` array.
3. If a match is found, access is granted, the relay is triggered, and a success message is displayed.
4. If the UID does not match, access is denied.

### RFID Tag Registration:
To add a new RFID tag to the system, you need to get its UID and update the `keyTagUID` array with the new UID.

### RFID Security Considerations:
Ensure that you register the UIDs of trusted RFID tags only. RFID systems can be vulnerable to cloning, so it is essential to implement additional security features like time-based or multifactor authentication for critical applications.

### RFID Reader Pin Configuration:
The RFID reader is connected to the ESP32 using SPI pins:
- **SS_PIN** = 10
- **RST_PIN** = 5

Make sure the correct pins are configured when initializing the RFID reader:

```cpp
MFRC522 rfid(SS_PIN, RST_PIN);
```

