>>>

# Sensor Station 

by: 
Shreenithi Madhavan, 28639
_Mechatronic Systems Engineering_

Shreshth Khosla, 28644
_Electrical and Electronics Engineering_

**Faculty of Technology and Bionics**

***Rhine-Waal University of Applied Sciences***

Date: 09 January 2023   

----
## Acknowledgment 

We would like to thank our Project Supervisors for guiding us through this project and giving us prompt and quality feedback. We would also like to thank our seniors/student assistants for giving us good advice based on their experience. This project is solely written by the members of Group 10 and all the references used are documented in the report.

Kleve, 
09.01.2023

Shreenithi Madhavan

Shreshth Khosla 


## Abstract



The project aims to measure the surrounding weather conditions from the sensor and successfully transmit the data to the OLED display through a microcontroller.
The board gets powered by a lithium battery which gives an output of 3.3V, safeguarded by a battery management system. We can recharge the battery using a mini USB which turns on an orange LED indicating a successful connection.
The microcontroller chip is programmed using an FTDI module communicating through an I2C interface. After the chip is successfully programmed, the OLED should display the measured values.



## Table of Contents

[[_TOC_]]

## 1 Introduction

A Weather Sensor station is an environmental monitoring device/gadget used to gather information regarding the current temperature, pressure & humidity. It plays a vital role in providing governments, businesses, and individuals with the data they need to stay prepared and respond appropriately during weather challenges. A sensor station can range from a simple household device dedicated to measuring the surroundings data to a complex gadget that measures the weather conditions & also predicts the numbers for the future. Hence, a sensor station is an indispensable device. 

### 1.1	Background

After finalizing the project idea, the research about the components, sensors, and chips began. The aim while choosing the parts was to focus on the features, cost efficiency, and long-activity. 
We also focused on buying THT parts, attached to the board using female headers, to repurpose in the future.

The project is built on a PCB using an ESP32-WROOM-32D chip, DHT22, an external temperature & Humidity sensor, and an OLED to display the values gathered using the sensor. The idea can be leveled-up by providing the data directly to a Wifi/BlueTooth-enabled device as an application of IoT.    


|FTDI module                  | FT232-AZ    |

|OLED panel                   | 0.96", OLED Display, SSD1306  |

|Temperature/Humidity sensor  |	RHT03_DHT-22_AM2302  |

|Microcontroller	          | ESP32-WROOM-32D   |
 
|Pushbutton (x4)              |     |

|Resistor SMD (x3)10k	      | R1206W  |

|Resistor SMD 2k	          | R1206W |

|Resistor SMD 470	          | R1206W |

|Capacitor SMD (x2) 10uF	  | C-EUC1206 |

|Capacitor SMD (x2) 4.7uF	  | C-EUC1206 |

|Temperature/Humidity sensor  |	RHT03_DHT-22_AM2302  |

|6 Pin Female Headers (x2)    |   | 

|Pushbutton (x4)              | FT232-AZ    |

|4 Pin Female Header          |   |

|LDO Voltage Regulators       | AZ1117CH-3.3TRG1 |

|LED2	                      | LEDSMT1206 |

|USBMINIB                     | USB BWM SMD   |

|Battery Management Li-Ion    | MCP73831T-5ACI/OT  |

|Battery Connector            | JST PH2P STS |

|Battery 	                  | Li-Ion 3.7V 500mAh |


## 2	Theory
(If necessary please present theory in this section.
This math is inline $`a^2+b^2=c^2`$.)

To understand how the PCB works, it is essential to be familiar with the working of some individual components. Brief explanantion of the parts is provided below:

•	**ESP 32-WROOM-32D**
This is the microcontroller used in our circuit. It is a powerful Wifi+Bluetooth MCU that can be used for a wide range of applications. The pin layout is provided below.

![esp32](/uploads/bb3f3d8b9b005fd032b4fe52bb4442f7/esp32.png)
•	**DHT22 Sensor**
We have used the DHT 22 sensor, made of a capacitive humidity sensor and a thermistor. It consists of a chip that does analog to digital conversion to display the temperature and humidity with the help of a microcontroller. It is ideal for displaying 0-100% humidity readings with 2-5% accuracy and -40 to 80°C temperature readings with ±0.5°C accuracy.

The pin layout of the sensor is :

Vcc	- Power supply 3.5V to 5.5V
Data -	Outputs both Temperature and Humidity through serial Data
NC -	No Connection and hence not used
Ground -	Connected to the ground of the circuit


•	**OLED Display**
As the name indicates, it displays the measured data through I2C communication. It conducts electricity to organic materials which are sandwiched between the electrodes for light emission.

The pins used of the OLED display are:

SDA – Serial data pin for I2C interface
SCL – Serial clock pin for I2C interface
GND – Ground pin
VCC – power supply for display


•	**FTDI Module**
The FTDI module uses an FTDI chip that converts the USB signal to a UART (Universal asynchronous receiver-transmitter) signal for our microcontroller to understand. It, therefore, aids in programming the ESP32-WROOM-32D chip using Arduino IDE.
The programming process is automatic. It uses two switches, BC547 transistors, and other components.
The RTS signal is for resetting the ESP and the DTR signal is to put the chip in flashing mode.

The pins used in the FTDI module is :

GND- Ground pin
RX- Input data reception
TX- Output data transmission
Vcc- Input data reception


•	**Voltage Regulator**
AMS1117-3.3 is a cost-efficient, low dropout (LDO) Voltage Regulator designed to provide a current of up to 1 ampere. The output voltage can vary from 1.5V to 5V. It also has a low dropout voltage of 1.3V when operating at maximum current. The maximum input voltage it can support is 15V. This device is used to obtain a steady voltage of 3.3V from the power source.

The pin layout is as follows:

ADJ/GND- Ground pin
Vout- Regulated output voltage
Input- Input voltage that has to be regulated


•	**Battery Management**
We use MCP73831 IC to charge the battery. It is an advanced linear charge management controller which is compact and budget friendly as well. It adopts a constant current/constant voltage circuit to prevent overcharging.

The pins layout is :
Vdd- Battery management input supply
Vbat- Battery charge control output
STAT- Charge status output
PROG- Current regulation set & charge control enable


## 3	Methodology

```
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Adafruit_Sensor.h>
#include <DHT.h>

#define SCREEN_WIDTH 128 // OLED display width, in pixels
#define SCREEN_HEIGHT 64 // OLED display height, in pixels
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

#define DHTPIN 19     // Digital pin connected to the DHT sensor

// Uncomment the type of sensor in use:
//#define DHTTYPE    DHT11     // DHT 11
#define DHTTYPE    DHT22     // DHT 22 (AM2302)
//#define DHTTYPE    DHT21     // DHT 21 (AM2301)
DHT dht(DHTPIN, DHTTYPE);


void setup() {
  Serial.begin(115200);

  dht.begin();

  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println(F("SSD1306 allocation failed"));
    for(;;);
  }
  delay(2000);
  display.clearDisplay();
  display.setTextColor(WHITE);
}

void loop() {
  delay(5000);

  //read temperature and humidity
  float t = dht.readTemperature();
  float h = dht.readHumidity();
  if (isnan(h) || isnan(t)) {
    Serial.println("Failed to read from DHT sensor!");
  }
  // clear display
  display.clearDisplay();
  
  // display temperature
  display.setTextSize(1);
  display.setCursor(0,0);
  display.print("Temperature: ");
  display.setTextSize(2);
  display.setCursor(0,10);
  display.print(t);
  display.print(" ");
  display.setTextSize(1);
  display.cp437(true);
  display.write(167);
  display.setTextSize(2);
  display.print("C");
  
  // display humidity
  display.setTextSize(1);
  display.setCursor(0, 35);
  display.print("Humidity: ");
  display.setTextSize(2);
  display.setCursor(0, 45);
  display.print(h);
  display.print(" %"); 
  
  display.display(); 
}

```

**Hardware**

**Design**


The design phase started as soon as the part list was ready. 

Initially, we intended to use the ESP32 Developer kit. But, since it was not allowed, we decided to use the raw chip. We researched to find a suitable method for programming. Accordingly, we designed our circuit and decided to use an FTDI module for programming the chip on Arduino IDE.

We started with the schematic design on Autodesk Eagle. The process took a couple of revisions to finalize the circuit design as we had to search the libraries and manually add them to Eagle according to the component requirements. We also did the ERC check where we had to rectify errors in the schematic.

After successfully eradicating and approving the errors in the schematic, we moved to the next task of building the board view using the schematic layout. While working with the board view, we spaced out the components far enough from each other to avoid congestion and ease the process while soldering. We also had to follow some steps/rules while designing the board view to manufacture the PCB in-house without any issues. We removed the DRC errors from the board view design.

**Manufacturing the PCB** 

•	**Printing** - We printed the board view design on a laminated plastic A4 sheet with the bottom and top layers mirrored to each other on the ends of the sheet.

•	**Cutting** - A board with copper plating is cut to the desired dimensions of the printout using a cutting machine for precision.

•	**PCB Masking** - The printed laminated is placed on the board for UV exposure.

•	**Uv exposure** - This board is placed under UV light for 2:30 minutes.

•	**Developing**- The board is placed in a solution of sodium hydroxide

•	**Etching**- The PCB is etched using iron chloride to get rid of the unprotected copper.

•	**Cleaning**- The PCB is wiped with alcohol to remove unwanted residue.

•	**Drilling** - After checking the connections between the tracks, we drilled holes for the THT components & the VIAS. It is used to connect the top & the bottom layer. The VIAS were punched down using a punching machine.

•	**Placing & Soldering** - Lastly, after successfully drilling the holes, we moved to the final step of soldering & mounting. We tried to put the SMD paste as precisely as possible to avoid spreading over other pads. We used the Pick and place machine to mount the SMD components of the PCB. The board with the components was put in the oven for 20 minutes to stick the parts firmly. Finally, we soldered the THT components after the previous task. 

After manufacturing the PCB, we realized that the connection from 10uF capacitor via voltage regulator to the power supply was incomplete. We tackled this problem by soldering a wire to complete the connection.

The placement of the mini-USB port was such that the cable for recharging the battery could not fit in the port. So, we cut the board such that the cable fits inside the port.


**Software**

The Coding part has been attached below




## 4 Results
(Here you should present your results.
This is an example how to include image:
![alt text](resources/Open_Source_Hardware_(OSHW)_Logo_on_blank_PCB.jpg "Example Image")
(C) Altzone, CC BY-SA 3.0 <https://creativecommons.org/licenses/by-sa/3.0>, via Wikimedia Commons)


The outcome of the project should look like this. 




## 5	Discussion


The final result of the project should display the values of Temperature & Humidity from the surroundings onto the OLED panel.


## 6	Concluding Comments


As a next step to our project, we could advance it to an IoT project. The values would be displayed directly to a device with Wifi/Bluetooth enabled by making changes to the code. We wish to make use of our board to the fullest. For the same reason, we have added six extra GPIOs for future growth.


## 7	References

* [1] https://www.mischianti.org/2021/05/26/esp32-wroom-32-high-resolution-pinout-and-specs/
* [2] https://learn.adafruit.com/dht
* [3] https://lastminuteengineers.com/oled-display-arduino-tutorial/
* [4] https://randomnerdtutorials.com/esp32-weather-station-pcb/
* [5] https://how2electronics.com/design-your-own-esp-board-for-battery-powered-iot-applications/
* [6] https://www.studiopieters.nl/esp32-program-a-esp32/
* [7] https://randomnerdtutorials.com/esp32-dht11-dht22-temperature-humidity-sensor-arduino-ide/
* [8] https://controls.hsrw.org/dokuwiki/doku.php?id=ee:pcbfoametching
* [9]	https://www.ourpcb.com/ftdi-pinout.html

## 8	Appendices

