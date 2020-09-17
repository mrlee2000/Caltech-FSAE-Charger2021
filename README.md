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
* Input is +12V from the charger box - needs to be stepped down to 3.3V for the microcontroller
* Buck converter takes in 12V and outputs 5V, linear regulator takes in 5V and outputs 3.3V
* From EE Lead, "Buck converters are much more efficient than linear regulators, but they are also much noisier. For a 24V to 5V 1A output buck converter, you'll tend to see efficiencies at 85%+ but with output voltage ripples at 3% or more. By using a buck converter in combination with a linear regulator, we can have most of the voltage drop occur in the high efficiency but noisy buck and the last of the voltage drop in the less efficient but clean linear regulator, so that we get the best of both methods and balance out their downsides"

The buck converter was designed using TI's Webench Program. The files generated are found in the TI_Webench_Buck folder.
 

Enable Line Circuitry
* Use a solid state relay to control line (low-side)
* See Enable Line schematic
 

Control Pilot Circuitry
* No ventilation, so R2 should not be changed
* Use optoisolator as detector, low side solid state relay
* See J1772 Schematic

Proximity Pilot
* R4 and R5 are suitable for our purposes
* See J1772 Schematic
