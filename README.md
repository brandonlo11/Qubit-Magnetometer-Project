# Qubit-Magnetometer-Project

**Goal: Detect and cancel out magnetic fields by altering current delivered to a three dimensional coil system.**

## Setup

Begin with an arduino nano and Adafruit LIS3MDL magnetometer. Connect the 5V and ground pins of the Arduino to the magnetometer, then connect the yellow wire to the SCL pin (A5) and the blue wire to the SDA pin (A4).

![IMG_4768](https://user-images.githubusercontent.com/103808636/218300295-f9ec9241-9cce-4f21-9010-2ccafed084e8.jpg)

Connect the vin+ and vin- ports of a transistor to the positive and negative terminal of a power source which has voltage and current. Connect the out+ and out- sides to the x-coil which creates the magnetic field. Then, solder wires to the pwm and signal ground parts of the transistor. Connect these to the D9 (pwm pin) of the Arduino Nano and ground. By fluctuating between values sent to the D9 pin, we can alter the amount of current being passed to the x direction of the coil.  Repeat these steps for the y and z coils.

![IMG_4773](https://user-images.githubusercontent.com/103808636/218300680-9de30219-63c3-4ff6-b224-07b8e9de1619.jpg)

To communicate code between your computer, Arduino and magnetometer download the Adafruit LIS3MDL library to the Arduino IDE.

## Code

To test the readings and see the current alteration by sending output to the pwm signals, I used the following code:

```
#include <Wire.h>
#include <Adafruit_LIS3MDL.h>
#include <Adafruit_Sensor.h>


Adafruit_LIS3MDL lis3mdl;
#define LIS3MDL_CLK 13
#define LIS3MDL_MISO 12
#define LIS3MDL_MOSI 11
#define LIS3MDL_CS 10


void setup(void) {
  pinMode(9, OUTPUT);
  pinMode(10, OUTPUT);  
  Serial.begin(115200);
  while (!Serial) delay(10);     // will pause Zero, Leonardo, etc until serial console opens


  Serial.println("Adafruit LIS3MDL test!");
 
  // Try to initialize!
  if (! lis3mdl.begin_I2C()) {          // hardware I2C mode, can pass in address & alt Wire
  //if (! lis3mdl.begin_SPI(LIS3MDL_CS)) {  // hardware SPI mode
  //if (! lis3mdl.begin_SPI(LIS3MDL_CS, LIS3MDL_CLK, LIS3MDL_MISO, LIS3MDL_MOSI)) { // soft SPI
    Serial.println("Failed to find LIS3MDL chip");
    while (1) { delay(10); }
  }
  Serial.println("LIS3MDL Found!");


  lis3mdl.setPerformanceMode(LIS3MDL_MEDIUMMODE);
  Serial.print("Performance mode set to: ");
  switch (lis3mdl.getPerformanceMode()) {
    case LIS3MDL_LOWPOWERMODE: Serial.println("Low"); break;
    case LIS3MDL_MEDIUMMODE: Serial.println("Medium"); break;
    case LIS3MDL_HIGHMODE: Serial.println("High"); break;
    case LIS3MDL_ULTRAHIGHMODE: Serial.println("Ultra-High"); break;
  }


  lis3mdl.setOperationMode(LIS3MDL_CONTINUOUSMODE);
  Serial.print("Operation mode set to: ");
  // Single shot mode will complete conversion and go into power down
  switch (lis3mdl.getOperationMode()) {
    case LIS3MDL_CONTINUOUSMODE: Serial.println("Continuous"); break;
    case LIS3MDL_SINGLEMODE: Serial.println("Single mode"); break;
    case LIS3MDL_POWERDOWNMODE: Serial.println("Power-down"); break;
  }


  lis3mdl.setDataRate(LIS3MDL_DATARATE_155_HZ);
  // You can check the datarate by looking at the frequency of the DRDY pin
  Serial.print("Data rate set to: ");
  switch (lis3mdl.getDataRate()) {
    case LIS3MDL_DATARATE_0_625_HZ: Serial.println("0.625 Hz"); break;
    case LIS3MDL_DATARATE_1_25_HZ: Serial.println("1.25 Hz"); break;
    case LIS3MDL_DATARATE_2_5_HZ: Serial.println("2.5 Hz"); break;
    case LIS3MDL_DATARATE_5_HZ: Serial.println("5 Hz"); break;
    case LIS3MDL_DATARATE_10_HZ: Serial.println("10 Hz"); break;
    case LIS3MDL_DATARATE_20_HZ: Serial.println("20 Hz"); break;
    case LIS3MDL_DATARATE_40_HZ: Serial.println("40 Hz"); break;
    case LIS3MDL_DATARATE_80_HZ: Serial.println("80 Hz"); break;
    case LIS3MDL_DATARATE_155_HZ: Serial.println("155 Hz"); break;
    case LIS3MDL_DATARATE_300_HZ: Serial.println("300 Hz"); break;
    case LIS3MDL_DATARATE_560_HZ: Serial.println("560 Hz"); break;
    case LIS3MDL_DATARATE_1000_HZ: Serial.println("1000 Hz"); break;
  }
 
  lis3mdl.setRange(LIS3MDL_RANGE_4_GAUSS);
  Serial.print("Range set to: ");
  switch (lis3mdl.getRange()) {
    case LIS3MDL_RANGE_4_GAUSS: Serial.println("+-4 gauss"); break;
    case LIS3MDL_RANGE_8_GAUSS: Serial.println("+-8 gauss"); break;
    case LIS3MDL_RANGE_12_GAUSS: Serial.println("+-12 gauss"); break;
    case LIS3MDL_RANGE_16_GAUSS: Serial.println("+-16 gauss"); break;
  }


  lis3mdl.setIntThreshold(500);
  lis3mdl.configInterrupt(false, false, true, // enable z axis
                          true, // polarity
                          false, // don't latch
                          true); // enabled!
}


void loop() {


  sensors_event_t event;
  /* Display the results (magnetic field is measured in uTesla) */
 
  analogWrite(9, 120);
  analogWrite(10, 120);  
  delay (2000);
  lis3mdl.getEvent(&event);  
  Serial.print("\nX: "); Serial.print(event.magnetic.x);
  Serial.print(" µT");
  Serial.print(" \tY: "); Serial.print(event.magnetic.y);
  Serial.print(" µT");
  Serial.print(" \tZ: "); Serial.print(event.magnetic.z);
  Serial.print(" µT");
  Serial.print(" \t|B|: "); Serial.print(sqrt(pow(event.magnetic.x, 2) + pow(event.magnetic.y, 2) + pow(event.magnetic.z, 2)));
  Serial.print(" µT");
  delay(3000);
 


  analogWrite(9, 0);
  analogWrite(10, 0);
  delay (2000);
  lis3mdl.getEvent(&event);  
  Serial.print("\nX: "); Serial.print(event.magnetic.x);
  Serial.print(" µT");
  Serial.print(" \tY: "); Serial.print(event.magnetic.y);
  Serial.print(" µT");
  Serial.print(" \tZ: "); Serial.print(event.magnetic.z);
  Serial.print(" µT");
  Serial.print(" \t|B|: "); Serial.print(sqrt(pow(event.magnetic.x, 2) + pow(event.magnetic.y, 2) + pow(event.magnetic.z, 2)));
  Serial.print(" µT");
 
  delay(2000);  


  delay(1000);
  Serial.println();
}
```
With this working, my goal was to now write code which would take these magnetic field readings and alter the pwm output in order to apply the correct amount of current for the magnetic field to be zero. 



## Applications of this Experiment

Manipulating magnetic fields has emerged as a powerful tool in the study of cellular biology and evolution. The magnetic field generated by the Earth has a significant impact on living organisms, and by altering the magnetic field in controlled experiments, scientists are able to gain insights into how changes in magnetic fields affect cellular processes. For example, studies have shown that magnetic fields can affect gene expression, influence the alignment of cells, and alter the behavior of organisms. This information is critical for understanding the evolutionary pressures that have shaped life on Earth and the mechanisms by which living organisms respond to changes in their environment, as well as how organisms may respond to various magnetic fields of space or other planets. By exploring the effects of magnetic fields on cellular biology, researchers can gain new insights into the fundamental processes that drive evolution and pave the way for new therapies and technologies that harness the power of magnetic fields to improve human health and well-being.
