# Zwift
HRM Controlled Fan with ESP32

First of all, a big shoutout to Jonathan (jludwig75) from whom I took inspiration for the code. Also, a huge thanks to the OpenAI team for their ChatGPT, which I extensively used.

It must be noted that I have NO background or specific knowledge about coding, nor do I know or understand anything about C++ or Python. I am just curious and have very basic electrical/electronics knowledge. Therefore, almost anybody could do this project.

What you need:

An ESP32 module (I used a NODEMCU module from Aliexpress, about 4 EUR) and its accessories (power, some cables).
A PWM Triac controller board. I bought one on Amazon (12 EUR), but there are many on Aliexpress (much cheaper). Any power will be enough (mine is one of the smallest with 600W capacity, and the fan is less than 100W). 
A 230VAC fan. Any cheap domestic 3-speed fan will work.
An HRM sensor. Any heart rate Bluetooth (BLE) band will work. I personally use a Polar H9.
A smartphone with the nRF Connect app to sniff the BLE connections and addresses (optional-the code is made to connect directly to heart rate monitor service).
An ANT+ key (optional). We will use the Bluetooth signal from the HRM band, so if you want to use Zwift or any other computer app, I strongly suggest separating the signals and using ANT+ to connect to the computer. You will not be able to use a tablet for Zwift or your cycling app (as they use Bluetooth only). Eventually, try to find a heart rate band with multipoint Bluetooth (able to connect with multiple devices), but I don't know if they exist.

Let's start with your HRM band on. Open nRF connect on your phone and connect to the band. Write down its MAC address and look through the services. You need to find the two services related to the heart rate. They should be 0x180D and 0x2A37. These values should be identical on all HRM bands. With this code, there's no need to indicate to a specific MAC address. The BLE module of the ESP32 will scan all the devices in range looking for UUID services 0x180D and 0x2A37.
This means a simpler connection, but also that the ESP32 will connect to any device which has those UUID in range. 

Connect the PWM board to pin 23 of the ESP32, and to 5VDC. 
From now on, BE CAREFUL WHEN WORKING WITH HIGH VOLTAGES! 
Connect the 230VAC to the VAC in of the PWM board. Connect the fan wires to the VAC out. Again, BE CAREFUL WHEN WORKING WITH HIGH VOLTAGES! RISK OF ELECTRIC SHOCK! 
Remember to always apply a load to the PWM. Basically, do not plug it in the mains without the fan connected. If your fan has 3 speeds, put it into the fastest one. You can eventually modulate. 

Connect your ESP32 to your PC and open ArduinoIDE. Copy/paste the code in the sketchbook and load it. It should work! 
You should see the on board LED of the ESP32 blinking, your BPM updated every second on the serial monitor of the IDE, the LEDs on the PWM board should light up and you should probably hear a noise from the fan (over 80 bpm). 
Here's the part of the code to modify according to your preferences.

// Regola la velocità del ventilatore in base al battito cardiaco
    // Modifica questa logica in base alle tue esigenze
    if (heartRate < 80) {
        analogWrite(PWM_PIN, 0); // Fan shut off
    } else if (heartRate >= 80 && heartRate < 100) {
        analogWrite(PWM_PIN, TRIAC_MAX * 0.25); // 25% of max speed over 80 BPM
    } else if (heartRate >= 100 && heartRate < 120) {
        analogWrite(PWM_PIN, TRIAC_MAX * 0.3); // 30% of max speed over 100 BPM
    } else if (heartRate >= 120 && heartRate < 130) {
        analogWrite(PWM_PIN, TRIAC_MAX * 0.4); // 40% of max speed over 120 BPM
    } else if (heartRate >= 130 && heartRate < 140) {
        analogWrite(PWM_PIN, TRIAC_MAX * 0.50); // 50% of max speed over 130 BPM
    } else if (heartRate >= 140 && heartRate < 150) {
        analogWrite(PWM_PIN, TRIAC_MAX * 0.65); // 60% of max speed over 140 BPM
    } else if (heartRate >= 150 && heartRate < 160) {
        analogWrite(PWM_PIN, TRIAC_MAX * 0.8); // 80% of max speed over 150 BPM
    } else {
        analogWrite(PWM_PIN, TRIAC_MAX); // 100% of max speed over 160 BPM

If it doesn't, I don't know why. I cannot be helpful. All the troubleshooting I did was made with ChatGPT. You may use it, too. 

I will update this code adding a status LED for the BLE connection (it's already in the code but did not test yet) and a remote reset button (as sometimes the connection may be lost). 
