## Caltech-FSAE-Charger2021 ##
EV Charger Board for 20-21 Caltech FSAE Team

Major jobs of the charger board:
  * Communicate with the BMS Master via CAN
  * Communicate with the charger box via CAN
  * Open the interlock if any faults occur on the charger side
  * More specifically, the board also does the following:
  * Handles the control pilot and proximity detection signals:
    * Control pilot signal - allows the board to determine how much power can be drawn from a specific power station
    * Proximity detection signal - allows the charger to know if the plug is being plugged into the car so charging to the car can be cut if the plug is not plugged in properly
  * Interface between the actual charging station and the car

Board sits in between charging unit and BMS, communicates with both via CAN
  * BMS is keeping track of how accumulator is charging, monitoring
  * Tells info to the charger board

Required to have charger as part of the interlock
  * Electrical system has BMS and battery
  * Before the charger reaches accumulator, has to go through two relays
  * Interlock is the power that goes to the relays
  * Accumulator Isolation Relays (AIRs)
  * Interlock goes through all the critical boards (BMS, I/O, Main, etc)
  * Each board has a solid state relay, which can open the line
  * When line opens, power is cut to AIRs - safety reasons
  * Interlock has to travel through charger board and safety switches
  * Safety button in the charger - opens interlock

Microcontroller (STM32F469: https://www.st.com/resource/en/datasheet/stm32f469ae.pdf)

We are using the HK-J-H440-20 charger : https://forums.aeva.asn.au/uploads/825/6.6KW_User_Manual.pdf

Signal control: CAN L, CAN H, CAN GND, 12V+, 12V-, Enable
  * CAN L, CAN H, and CAN GND are used to communicate with the board via CAN
  * +12V and -12V are used to power the board
  * Enable
    * Enables the charger board (See Schematics Folder for picture)

We are using the J1772 plug to connect to the car: https://www.ti.com/lit/ug/tiduer6/tiduer6.pdf?ts=1596320029937&ref_url=https%253A%252F%252Fwww.google.com%252F

Control pilot signal - allows the board to determine how much power can be drawn from a specific power station
  * Level 2 allows more power to be drawn - varies by power station
  * PWM signal - frequency tells how much power - determine if can meet power needs, can do something to line to tell it to begin charging

Proximity detection signal - allows the charger to know if the plug is being plugged into the car so charging to the car can be cut if the plug is not plugged in properly

System Connections
* Connected to other subsystems through CAN
* Connections to the BMS Master and the Charger Box
 

Power Circuitry
* 24V comes from an external battery - since the board is not on the vehicle we have to power it externally
* Buck converter takes 24V and steps it down to 5V
  * Very efficient (almost 80%) but noisy
  * Buck was designed using TI Webench
* Linear Regulator takes 5V and steps it down to 3.3V
  * Less efficient, but not noisy
* Try to balance the pros of both converters with their cons


The buck converter was designed using TI's Webench Program. The files generated are found in the TI_Webench_Buck folder.
 

Enable Line Circuitry
* Using a mechanical relay to open and close the enable line
  * Open and closed by the microcontroller
* Mechanical bc we're not 100% sure what the ground reference of the enable relay - block diagram is unclear
* Have an LED in parallel so we know when the line is closed
* See Enable Line schematic
 

Control Pilot Circuitry
* Control Pilot is a 12V signal 
  * 2.74k resistor drops it to 9V when the charger is connected to the charging station - activates the wave generator which will transmit the PWM signal we want to read
  * Charging is activated when the 1.3k resistor is connected to ground - drops to 6V
* No ventilation for our vehicle
* Using an op amp in a buffer configuration (2020-10-01: The op amp currently on the schematic will change)
  * Meant to source the current necessary to drive the output without drawing current from the input
  * Station is sensitive to changes in voltage - don’t want to induce a voltage drop
  * Voltage divider to step from 5V output of op amp to 3.3V needed for microcontroller
* See J1772 Schematic

Proximity Pilot
* Charging station measures differences between the proximity detection and the control pilot
* Resistor values are SAE standard
* On station - button, connects PP to GND
  * When the charger board sees more voltage drop across the 2.74k resistor, knows that the station will be disconnected - stop current flow
  * Avoids arcing 
* See J1772 Schematic
