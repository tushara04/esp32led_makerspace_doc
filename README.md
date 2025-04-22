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

At this point, the software setup is pretty much done. Only the code needs to be setup:

```
your mom
```

## Acknowledgement
The project was carried out with the help of the Makerspace Lab team at Ashoka University on 21st April 2025.

## References
1. ESP32 documentation: https://docs.espressif.com/projects/arduino-esp32/en/latest/getting_started.html
2. ESP32 image: https://wiki.seeedstudio.com/XIAO_ESP32C3_Getting_Started/
3. Makerspace documentation of the build: https://docs.google.com/document/d/1ILUwVJau7dqHt7u987nV3i2fWaezmoz3xmDAD6VhAEY/edit?tab=t.0
3. 

---
