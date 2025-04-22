# ESP32_LED Documentation

## Project Description
The project involves using an ESP32, a mini development board, or more formally, a single 2.4 GHz wifi-and-bluetooth SoC (System on a Chip)<sup>1</sup>,  to control a little LED using Alexa. The motivation behind the project is to get the user introduced to the idea of Do-It-Yourself Internet of Things (DIY IoT), wherein we use off-the-shelf components and open source tools to build devices that connect to the internet, enabling a remote access and a transfer of data or commands, among other things. All the resources used are tagged in-text and mapped to the respective sources in the reference section in order.

## Milestones
1. Set up the software: install the Arduino IDE, the necessary packages, and set up the code.
2. Set up the hardware: connect the ESP and the LED to the breadboard and the entire setup to the laptop for powersource.
3. Testing the prototype: use Alexa to turn on or off the LED.
4. Further tweaks: edit the code to play around with the LED further.

## Materials Used
1. ESP32: an IoT mini development board with a 32bit RISC-V singlecore processor operational upto 160 MHz.<sup>2</sup>
![Design of an ESP32](/images/esp_merged.jpg) 
2. Blue LED: to be controlled through Alexa.
3. Solderless Breadboard: used to temporarily connect the ESP to the LED.
4. USB A-C: Used to connect the ESP32 to the laptop and transfer the code to the ESP. It is also used to allow for the laptop to act as a powersource for the circuit.
5. Laptop with Arduino IDE installed: ASUS VivoBook Laptop with AMD Ryzen 7 4700U processor, 8.0 GiB RAM, 1.0 TB SSD storage, Arch Linux-based EndeavourOS, Arduino IDE 2.3.6.

## Construction <sup>3</sup>
### Setting up the software: 
After Arduino IDE is installed from the ![official website](https://www.arduino.cc/maker), install ESP32 board manager, provided by Espressif Systems, from a toolbar on the left of the IDE. This is the Arduino core for the ESP32, which allows for the IDE to work with the ESP we have. 

> **note: some guides, including the official one, suggests putting a release link into the Additional Board Manager URLs field in the Preference section where the IDE looks for the packages, but what we need are now natively present on the IDE's Board Manager and Library Manager, so this step is inessential.**

After done installing, plug the ESP to the computer, and click on "Select Board" dropdown in the top panel, then "Select other board and port...", and find "XIAO_ESP32C3" (our ESP model) and the port the ESP is connected to. Choosing the correct board and port allow for the compilation and upload of the code/sketch to the ESP.

> **tip: slow wifi interrupts the process of downloading the packages and causes it to get stuck; good wifi connection is recommended to save time.**

Finally, from the Library Manager, on the left toolbar, install "FauxmoESP” by Paul Vint and "Async TCP” by ESP32Async. FauxmoESP enables the Amazon Alexa support for the ESP32, wherein the device can be voice controlled using Amazon Echo. Async TCP enables a network environment for the ESP32.

At this point, the software setup is pretty much done; only the code needs to be setup:

```
#include <Arduino.h>
#ifdef ESP32
#include <WiFi.h>
#else
#include <ESP8266WiFi.h>
#endif
#include "fauxmoESP.h"

fauxmoESP fauxmo;

// -----------------------------------------------------------------------------
// ADD WIFI CREDENTIALS HERE; replace the ... with input
#define WIFI_SSID "..."
#define WIFI_PASS "..."

#define SERIAL_BAUDRATE 115200

#define LED_PIN ...  // This is to tell the ESP which Physical pin the LED is connected to. Use the Pin number as an integer or Pin name as a String.

#define ID_MY_LED "..."  // You can change what you call your device; this is how Alexa will identify it

// -----------------------------------------------------------------------------

// -----------------------------------------------------------------------------
// Wifi
// -----------------------------------------------------------------------------

void wifiSetup() {

  // Set WIFI module to STA mode
  WiFi.mode(WIFI_STA);

  // Connect
  Serial.printf("[WIFI] Connecting to %s ", WIFI_SSID);
  WiFi.begin(WIFI_SSID, WIFI_PASS);

  // Wait
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(100);
  }
  Serial.println();

  // Connected!
  Serial.printf("[WIFI] STATION Mode, SSID: %s, IP address: %s\n", WiFi.SSID().c_str(), WiFi.localIP().toString().c_str());
}

void setup() {

  // Init serial port and clean garbage
  Serial.begin(SERIAL_BAUDRATE);
  Serial.println();
  Serial.println();

  // PINs
  pinMode(LED_PIN, OUTPUT);
  digitalWrite(LED_PIN, LOW);

  // Wifi
  wifiSetup();

  // By default, fauxmoESP creates it's own webserver on the defined port
  // The TCP port must be 80 for gen3 devices (default is 1901)
  // This has to be done before the call to enable()
  fauxmo.createServer(true);  // not needed, this is the default value
  fauxmo.setPort(80);         // This is required for gen3 devices

  // You have to call enable(true) once you have a WiFi connection
  // You can enable or disable the library at any moment
  // Disabling it will prevent the devices from being discovered and switched
  fauxmo.enable(true);

  // You can use different ways to invoke alexa to modify the devices state:
  // "Alexa, turn yellow lamp on"
  // "Alexa, turn on yellow lamp
  // "Alexa, set yellow lamp to fifty" (50 means 50% of brightness, note, this example does not use this functionality)

  // Add virtual devices
  fauxmo.addDevice(ID_MY_LED);

  fauxmo.onSetState([](unsigned char device_id, const char* device_name, bool state, unsigned char value) {
    // Callback when a command from Alexa is received.
    // You can use device_id or device_name to choose the element to perform an action onto (relay, LED,...)
    // State is a boolean (ON/OFF) and value a number from 0 to 255 (if you say "set kitchen light to 50%" you will receive a 128 here).
    // Just remember not to delay too much here, this is a callback, exit as soon as possible.
    // If you have to do something more involved here set a flag and process it in your main loop.

    Serial.printf("[MAIN] Device #%d (%s) state: %s value: %d\n", device_id, device_name, state ? "ON" : "OFF", value);

    // Checking for device_id is simpler if you are certain about the order they are loaded and it does not change.
    // Otherwise comparing the device_name is safer.

    if (strcmp(device_name, ID_MY_LED) == 0) {
      digitalWrite(LED_PIN, state ? HIGH : LOW);
    }
  });
}

void loop() {

  // fauxmoESP uses an async TCP server but a sync UDP server
  // Therefore, we have to manually poll for UDP packets
  fauxmo.handle();

  // This is a sample code to output free heap every 5 seconds
  // This is a cheap way to detect memory leaks
  static unsigned long last = millis();
  if (millis() - last > 5000) {
    last = millis();
    Serial.printf("[MAIN] Free heap: %d bytes\n", ESP.getFreeHeap());
  }

  // If your device state is changed by any other means (MQTT, physical button,...)
  // you can instruct the library to report the new state to Alexa on next request:
  // fauxmo.setState(ID_MY_LED, true, 255);
}
```
Once the code has been edited appropriately as instructed in comments, and it is put on the IDE, the software set up is finished.

### Setting up the hardware:


## Acknowledgement
The project was carried out with the help of the Makerspace Lab team at Ashoka University on 21st April 2025.

## References
1. ESP32 documentation: https://docs.espressif.com/projects/arduino-esp32/en/latest/getting_started.html
2. ESP32 image: https://wiki.seeedstudio.com/XIAO_ESP32C3_Getting_Started/
3. Makerspace documentation of the build: https://docs.google.com/document/d/1ILUwVJau7dqHt7u987nV3i2fWaezmoz3xmDAD6VhAEY/edit?tab=t.0
3. 

---
