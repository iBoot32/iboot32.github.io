Creating a robot to solve Rubik's Cubes in under 2 seconds
By Tom O'Donnell (@iBoot32 on Twitter)

Materials:

- Arduino Mega
- CNC Shield v3
- Small Breadboard
- Access to a 3D Printer
- Six 17HS4401 Motors (NEMA 17)
- Six DRV8825 Motor Drivers with heatsinks
- A bunch of jumper wires, screws, and washers

_______________________________
Step 1: Assemble the CNC Shield
-------------------------------


### For this tutorial, use [my circuit diagram](https://imgur.com/TsPxkm0) as reference.

First, place the CNC Shield correctly on the Arduino. 
From here, insert four DRV8825 drivers into the elevated pins on the shield. If you old the Arduino so power terminal is on the right side, the heatsink for the driver should be towards the top of the shield (as pictured in the circuit diagram).

Now, take a short jumper, and jump the EN/GND pins together on the south-east end of the shield. Also directly north of the EN/GND pins, you'll see a series of pins labeled "X, Y, Z, D12" or something similar. Jump together the two blue pins labeled "D12" and the two yellow pins directly next to those. This will allow the fourth driver to operate independently of the other three, connected to pins D12 and D13 of the Arduino.

Finally, hook the shield's power terminal up to a 12V power supply, and connect each motor's wires to their own driver on the shield. The exact wiring depends on your motor, but my wiring diagram has the correct wiring for the 17HS4401 motor. 

____________________________
Step 2: Wire the breadboard
----------------------------
First, take a jumper from the 5v pin on the south-east end of the shield, and jump it to the BOTTOM positive row on your breadboard. Also jump the shield's GND to the BOTTOM ground row on your breadboard. The top +/- rows will be for 12v power from the wall, not from the shield. 

For powering the drivers, I used a 12V 4a power supply connected to the top power rail of the breadboard. Just make sure it has enough power to operate the drivers, but not enough to catch the breadboard on fire.

From here, the wiring is very difficult to explain, so refer to the circuit diagram. Make sure NOT TO MESS IT UP. Also, include a 100uF capacitor right after the wall power supply. This will help prevent damage due to voltage spikes. 

The most important part is that you take note of which driver connects to which digital pins on the arduino. You will need that information for interfacing the motors later. 

** TRIPLE CHECK YOUR WORK, SINCE ACCIDENTALLY SHORTING 12V/5V TO GND WILL CAUSE YOUR ARDUINO TO CATCH ON FIRE AND FRY YOUR WHOLE SETUP! **

________________________
Step 3: The actual robot
------------------------

Design an enclosure to house the motors and cube. You can copy this project's design or create your own. The important part is each motor needs to attach to its own side on the cube. This project's enclosure is a series of panels, each 7mm thick, that has one motor screw onto it. 

The CAD for this project is still a work-in-progress, so contact @iBoot32 if you would like the SLDPRT files. 

Now, you'll need a way to connect each motor to a face on the cube. This project uses little adapters that fir snugly onto the motor shaft, and have a flat end superglued to a center cap on the Rubik's Cube. 

NOTE: THE CONNECTION TO THE MOTOR SHAFT MUST BE EXTREMELY SECURE, OTHERWISE YOU WILL ENCOUNTER ISSUES IN TURNING ACCURACY. I RECOMMEND SUPERGLUING THE ADAPTER PIECE YOU DESIGN TO THE MOTOR SHAFT.

__________________________________________
Step 4: Set the power limit on each driver
-------------------------------------------

This is easily the most important step of the whole process. If you forget to set the power limit correctly, you will absolutely catch the breadboard on fire, since they can only support 1-1.5 amps AT MOST. 

On each driver, there's a little screw (current-limiting potentiometer) that controls the voltage limit. 

Google your motor's rated amperage, and use this formula to determine the correct voltage limit:

`Voltage = Rated_current / 2`

With the CNC shield, arduino, and breadboard powered up, connect the red lead of a multimeter to the current-limiting potentiometer, and the GND lead to the GND pin on the driver (if the potentiometer is on the bottom-left, the GND pin is the top-right pin). Take note of the current voltage, and use a screwdriver to adjust the potentiometer until the voltage limit matches your desired limit from the above formula. 

_________________
Step 5: The code
-----------------

Take note of which digital pins on the arduino are connected to the drivers on the breadboard. 

As for which digital pins are connected to the CNC Shield: 

- X.STEP is connected with the Arduino’s pin number 2.
- Y.STEP is connected with the Arduino’s pin number 3.
- Z.STEP is connected with the Arduino’s pin number 4.
- X.DIR is connected with the Arduino’s pin number 5
- Y.DIR is connected with the Arduino’s pin number 6
- Z.DIR is connected with the Arduino’s pin number 7

You may be wondering what STEP and DIR refer to. The DIR pin controls the direction the motor will spin. Setting it to HIGH results in a clockwise turn, LOW results in counterclockwise. The STEP pin is where you will send your HIGH pulses to make the motor move forward.

I will not provide the full code, as it's dependent heavily on your setup. However, use this as reference:

```
// Define stepper motor connections and steps per revolution:
#define STEP_PIN_F 2
#define DIR_PIN_F 5

#define DELAY 800
int turn = 50;

void setup() {
  pinMode(STEP_PIN_F, OUTPUT);
  pinMode(DIR_PIN_F, OUTPUT);

  digitalWrite(DIR_PIN_F, HIGH);
  for(int x = 0; x < turn; x++)
  {
    digitalWrite(STEP_PIN_F, HIGH);
    delayMicroseconds(DELAY);
    digitalWrite(STEP_PIN_F, LOW);
    delayMicroseconds(DELAY);
  }

void loop(){
}
```

_________________________
Step 6: Solving Mechanism
-------------------------

You should now be able to interface all six drivers independently of each other. 

The next step is to choose a solving algorithm. The rest will be left mainly to the reader, since there are hundreds of ways to implement a solving program.

One way is to use Kociemba's Cube Explorer on PC, then have a function to translate the solution from <R, L, U, D, F, B> form to moves on the motor and send them over serial. You could also use a command-line solver like ksolve. Both solutions are good. 

________
Wrap-up
--------

If all goes well, you should have a functioning cube-solving robot now. Congrats!

If you have any questions along the way, feel free to contact iBoot32 on Twitter and they'll answer any questions you have.

# DEMO:

[ROBOT BREAKS RUBIK’S CUBE WORLD RECORD](https://youtu.be/_Nr04z7VCYc)
