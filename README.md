# ENES100 FAQ Table of Contents
1. [Why is my simulator code not working on my OTV?](#q_sim)
2. [Why are my pointers not working in the simulator?](#q_pointers)
3. [Why is my OTV not turning to the direction I want it to?](#q_direction)
4. [How do I get my servo motor to stop jittering?](#q_jitter)
5. [Why is my OTV just driving in a circle in the arena?](#q_circle)
6. [Why is my sensor or motor not responding to my code?](#q_motor_response)
7. [Why is my sensor working but not being triggered by the expected action?](#q_sensor_action)
8. [Why isn't my WiFi module working?](#q_esp)
9. [How do I check for shorts in my circuit?](#q_short)
10. [Why is my soldering iron not working/why can I not solder?](#q_solder)
11. [Why is my laser cut not working/the wrong size?](#q_laser)
12. [Why are my servos, motors, and or other linear actions (components which run continuously) not working even though enough voltage is being applied?](#q_servo_voltage)
13. [When I try to use pinMode() on the Tx and Rx pins, why does the wifi module does not work?](#q_wifi_pin)
<!---  1. [Sub paragraph](#subparagraph1)
 3. [Another paragraph](#paragraph2) -->

#### <span style="color:red">Q. Why is my simulator code not working on my OTV?</span> <a name="q_sim"></a>

A. The simulator code uses the Tank.h library, which is designed to only run with the simulator. You will have to make your own motor controller code for your OTV to work.

#### <span style="color:red">Q. Why are my pointers not working in the simulator?</span> <a name="q_pointers"></a>

A. The simulator does not support pointers.

#### <span style="color:red">Q. Why is my OTV not turning to the direction I want it to?</span> <a name="q_direction"></a>

A. Try to verify the arena coordinates your OTV is in and that you're asking it to turn to the correct angle. In addtion, add +- thresholds to target angle. For example, if you have a target angle of 90°, set a range between 85° and 95°. In addition, make sure that you are sampling the coordinates frequently enough. Try to sample the coordinates every 20 milliseconds in order to get a consistent update on your OTV location.

#### <span style="color:red">Q. How do I get my servo motor to stop jittering?</span> <a name="q_jitter"></a>

A. Try detaching the servo using the “servo.detach()” method. You should only have the servo “attached” (use servo.attach()) when the OTV is actually executing the mission. Before and after that, unless the servo is needed, it should be “detached”. View example below:

```
#include <Servo.h>  
  
Servo myservo;  
  
void setup()  
{  
  myservo.attach(9);  
  // here 9 is the corresponding pin for the servo  
}  
  
void loop() {  
  myservo.detach  //detach it while navigation to mission site  
  myservo.attach  //attach the servo when executing the mission or anything which requires the servo  
  myservo.detach  //detach it when done  
}

```
#### <span style="color:red">Q. Why is my OTV just driving in a circle in the arena?</span> <a name="q_circle"></a>

A. Check your code. If there is no apparent error, try adding an extra delay so that the system has enough time to fully process. For the location of the delay, view sample below:


<pre>
<font color="#5e6d03">#include</font> <font color="#434f54">&lt;</font><b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#000000">h</font><font color="#434f54">&gt;</font>

<font color="#434f54">&#47;&#47;global</font>
<font color="#00979c">float</font> <font color="#000000">x</font><font color="#434f54">,</font> <font color="#000000">y</font><font color="#434f54">,</font> <font color="#000000">theta</font> <font color="#434f54">=</font> <font color="#000000">0.0</font><font color="#000000">;</font>

<font color="#00979c">void</font> <font color="#5e6d03">setup</font><font color="#000000">(</font><font color="#000000">)</font> <font color="#000000">{</font>
 &nbsp;&nbsp;<b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#d35400">begin</font> <font color="#000000">(</font><font color="#005c5f">&#34;It&#39;s lit&#34;</font><font color="#434f54">,</font> <font color="#000000">FIRE</font><font color="#434f54">,</font> <font color="#000000">3</font><font color="#434f54">,</font> <font color="#000000">8</font><font color="#434f54">,</font> <font color="#000000">9</font><font color="#000000">)</font><font color="#000000">;</font>
 &nbsp;&nbsp;<font color="#434f54">&#47;&#47; Valid mission names include: CRASH_SITE, DATA, MATERIAL, FIRE, and WATER</font>
 &nbsp;&nbsp;<font color="#434f54">&#47;&#47; These are case sensitive.</font>
 &nbsp;&nbsp;<font color="#434f54">&#47;&#47; The numbers are the pins used for RX, TX, and power.</font>
 &nbsp;&nbsp;<b><font color="#d35400">Serial</font></b><font color="#434f54">.</font><font color="#d35400">begin</font><font color="#000000">(</font><font color="#000000">9600</font><font color="#000000">)</font><font color="#000000">;</font>
<font color="#000000">}</font>

<font color="#00979c">void</font> <font color="#5e6d03">loop</font><font color="#000000">(</font><font color="#000000">)</font> <font color="#000000">{</font>
 &nbsp;<font color="#5e6d03">if</font><font color="#000000">(</font><font color="#434f54">!</font><b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#d35400">updateLocation</font><font color="#000000">(</font><font color="#000000">)</font><font color="#000000">)</font><font color="#000000">{</font><b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#000000">printIn</font><font color="#000000">(</font><font color="#005c5f">&#34;Error&#34;</font><font color="#000000">)</font><font color="#000000">;</font>

<font color="#000000">}</font>
<font color="#5e6d03">else</font><font color="#000000">{</font>
 &nbsp;<font color="#000000">x</font> <font color="#434f54">=</font> <b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#000000">location</font><font color="#434f54">.</font><font color="#000000">x</font><font color="#000000">;</font>
 &nbsp;<font color="#000000">y</font> <font color="#434f54">=</font> <b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#000000">location</font><font color="#434f54">.</font><font color="#000000">y</font><font color="#000000">;</font>
 &nbsp;<font color="#000000">theta</font> <font color="#434f54">=</font> <b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#000000">location</font><font color="#434f54">.</font><font color="#000000">theta</font><font color="#000000">;</font>
 &nbsp;<font color="#434f54">&#47;&#47; This is where you might want to add a delay so that you can read what is printed and so the system has enough time to register the angle.</font>
 &nbsp;<b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#d35400">print</font><font color="#000000">(</font><font color="#000000">x</font><font color="#000000">)</font><font color="#000000">;</font><font color="#434f54">&#47;&#47; These print to the vision system</font>
 &nbsp;<b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#d35400">print</font><font color="#000000">(</font><font color="#005c5f">&#34;,&#34;</font><font color="#000000">)</font><font color="#000000">;</font>
 &nbsp;<b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#d35400">print</font><font color="#000000">(</font><font color="#000000">y</font><font color="#000000">)</font><font color="#000000">;</font>
 &nbsp;<b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#d35400">print</font><font color="#000000">(</font><font color="#005c5f">&#34;,&#34;</font><font color="#000000">)</font><font color="#000000">;</font>
 &nbsp;<b><font color="#d35400">Enes100</font></b><font color="#434f54">.</font><font color="#d35400">println</font><font color="#000000">(</font><font color="#000000">theta</font><font color="#000000">)</font><font color="#000000">;</font>
 &nbsp;<b><font color="#d35400">Serial</font></b><font color="#434f54">.</font><font color="#d35400">print</font><font color="#000000">(</font><font color="#000000">x</font><font color="#000000">)</font><font color="#000000">;</font><font color="#434f54">&#47;&#47; These print to the serial monitor from the Arduino program on your computer</font>
 &nbsp;<b><font color="#d35400">Serial</font></b><font color="#434f54">.</font><font color="#d35400">print</font><font color="#000000">(</font><font color="#005c5f">&#34;,&#34;</font><font color="#000000">)</font><font color="#000000">;</font>
 &nbsp;<b><font color="#d35400">Serial</font></b><font color="#434f54">.</font><font color="#d35400">print</font><font color="#000000">(</font><font color="#000000">y</font><font color="#000000">)</font><font color="#000000">;</font>
 &nbsp;<b><font color="#d35400">Serial</font></b><font color="#434f54">.</font><font color="#d35400">print</font><font color="#000000">(</font><font color="#005c5f">&#34;,&#34;</font><font color="#000000">)</font><font color="#000000">;</font>
 &nbsp;<b><font color="#d35400">Serial</font></b><font color="#434f54">.</font><font color="#d35400">println</font><font color="#000000">(</font><font color="#000000">theta</font><font color="#000000">)</font><font color="#000000">;</font>

<font color="#000000">}</font>

</pre>

#### <span style="color:red">Q. Why is my sensor or motor not responding to my code?</span> <a name="q_motor_response"></a>

A. Isolate the code that you intend to use for the motor/sensor and place it in a new sketch. This can verify that your commands work and are pointing to the correct pins. If it does not work, verify that you have the correct pins and that the amount of voltage you predict to flow to a specific point is correct (view multimeter FAQ if necessary). Modify code and wiring as needed. If that also doesn’t work, try to reintegrate earlier parts of the loop to see if your code is getting stuck somewhere else. Rinse and repeat.


#### <span style="color:red">Q. Why is my sensor working but not being triggered by the expected action?</span> <a name="q_sensor_action"></a>

A. Consider lowering the threshold (or distance) at which the sensor is activated. This increases the relative sensitivity of the sensor.

#### <span style="color:red">Q. Why isn't my WiFi module working?</span> <a name="q_esp"></a>

Use these pins for __Arduino Megas__

- 50
- 51
- 52
- 53

A. __Do not use pins 0 and 1 on your Arduino.__
	  
There could be many reasons why. First check to see if there is a red light on the wifi module. If there is, that means it is getting power (not necessarily the correct amount). If the red light is very dim, check to make sure that you’re giving the WiFi module 5V power (not 3.3V). If it’s still dim, your Arduino may be supplying too much current to other devices on your OTV so they should be powered from your battery instead.

*If there isn’t a red light*, check your circuit to see if any wires are disconnected or placed incorrectly. If that is corrected and there is still no red light, then use a multimeter to check to see if the wifi module is actually receiving the correct voltage of 5V.

Turn the multimeter to 20V (this is because the wifi module requires 5V and the voltage of the multimeter should be higher than that) and then put one probe on the ground source (the wire or port which the wifi module is connected to) for the wifi module and the other on the power source. Make sure the battery is connected, the kill switch is on, and the Arduino is not connected to a computer or power source other than the battery. The reading on the multimeter should be ~5V. If it is not, the wifi module is not receiving the correct amount of power. This means an error in the circuit.


*If there isn’t a blue light*, that means that the WiFi module is not communicating with the vision system. First, do not use pins 0 or 1 (despite them being labeled TX and RX); on most microcontrollers, any digital pin except 0 and 1 should work. If you are using an Arduino Mega, check to see that TX is in 50, 51, 52, or 53. If TX is not in one of those, switch that and change the code accordingly. If all these conditions are met and the blue light is still not blinking, the problem is likely in your code. Is the program only running once in the setup() function? Make sure that there is something in the setup that is not in loop and that there is something in both. You could also try running just the example code from the ENES100 library (found on the website) to check and see if the wifi module is working.



#### <span style="color:red">Q. How do I check for shorts in my circuit?</span> <a name="q_short"></a>

A. Grab a multimeter, from one of the cabinets in the lab, and turn the dial to “continuity”. The symbol for “continuity” is highlighted by the red arrow below.

<p align="center">
  <img src="https://raw.githubusercontent.com/umdenes100/VisionSystemRemoteClient/master/img/multimeter.png" alt="Multimeter" width="250">

  <p>Place one probe on a power component of the circuit and one on a ground component of the circuit. Both probes must touch the metal part of the component. If the multimeter beeps, the power and ground must be touching somewhere. 

Repeat the process on different power and ground wires until you locate the exact location of the short.

#### <span style="color:red">Q. Why is my soldering iron not working/why can I not solder?</span> <a name="q_solder"></a>

*Step 0:* Check the condition of the tip of the iron. If it is very black, replace the tip (an example of a bad tip is pictured below). This is done by unscrewing the black grip of the soldering iron and removing the tip. Select a new one and insert it back into the iron. Screw the grip back into place over it. Do NOT do this with the iron plugged in or if the iron is hot!

<p align="center">
  <img src="https://raw.githubusercontent.com/umdenes100/VisionSystemRemoteClient/master/img/Solder_Tips.png" alt="Solder Tips + Order" width="450">
	<p>
	
*Step 1:* Turn the soldering iron on to 450 F and wait until you see that the small number (green arrow) matches the larger one (red arrow)

<!--<p align="center">
  <img src="https://raw.githubusercontent.com/umdenes100/VisionSystemRemoteClient/master/img/Solder_Temp.png" alt="Temperature Adjustment" width="250">

  <p> -->

*Step 2*: Make sure that the sponge under the iron is wet and that the fan for your iron is on

  <p align="center">
  <img src="https://raw.githubusercontent.com/umdenes100/VisionSystemRemoteClient/master/img/Sponge_Water_Vent_Fan.png" alt="Sponge, Water, & Vent Fan" width="450">
  <p>

  

*Step 3*: Make sure your wire is set up correctly. Both wires you are soldering should have the ends stripped about 0.5 inches. These should then be twisted together and mounted in the clips (helping hands) as shown below:

<p align="center">
  <img src="https://raw.githubusercontent.com/umdenes100/VisionSystemRemoteClient/master/img/Helping_Hands.png" alt="Helping Hands" width="450">
  <p>
	  
*Note: Both wires should be partially connected thanks to the twisting and securely held by the clips so that they do not separate while you are soldering.*

  
  *Step 4*: Plunge the soldering iron tip into the tip tinner (pictured below). The tip tinner should bead up along the tip. After this, shove the entire tip into the steel wool (pictured below) and rotate it around. To remove any beads of solder but keep a thin layer behind. Then, use a brush to paint the flux onto your wires.

<p align="center">
  <img src="https://raw.githubusercontent.com/umdenes100/VisionSystemRemoteClient/master/img/Soldering_Accessories.png" alt="Soldering Accessories" width="450">
  <p>
 
  

*Step 5*: To help ensure a strong solder connection, heat up the wires before begining soldering. To do this, touch the soldering iron tip to the wires and hold it there. It should take a few second for the wire to heat up. This will take longer for thicker gauge wire.

Once the wires are hot, unwind the solder (pictured below) and feed the solder into the section which you just preheated. If your wires have been preheated and you have both the solder and the iron in contact with the wire, the solder should melt, flow along twisted wires, and then cool.
	 
Alternatively, for larger connections, after melting some solder onto the iron tip, touch the end of the solder to the wire connection and touch the iron to the same connection. Heating up the connection will melt the solder directly onto the connection.

  <p align="center">
  <img src="https://raw.githubusercontent.com/umdenes100/VisionSystemRemoteClient/master/img/Solder.png" alt="Solder" width="250">
  <p>
  

*Step 6*: To test to see if the soldering job was done correctly: pull the wires apart. If they separate, try again. You can also check for connectivity with the multimeter (view Q: How do I find shorts in my circuit).


#### <span style="color:red">Q. Why is my laser cut not working/the wrong size?</span> <a name="q_laser"></a>

A. For if it’s the wrong size, make sure the dimensions are 1:1 when exporting from Fusion360. You should also make sure that the style in Fusion360 is “line” (view screenshot)

  <p align="center">
  <img src="https://raw.githubusercontent.com/umdenes100/VisionSystemRemoteClient/master/img/Laser_Cutter.png" alt="Laser Cutter Settings" width="250">
  <p>

The mouse is over the correct selection for style 

Make sure to delete any text boxes and text before exporting as a pdf as well.

If the cut stops partway through, check that the piece of acrylic or birch is not right up to the edges of the laser bed. If it is, move it away from the edges by about 2 inches and start over.


#### <span style="color:red">Q. Why are my servos, motors, and or other linear actions (components which run continuously) not working even though enough voltage is being applied?</span> <a name="q_servo_voltage"></a>

A. Check for common ground! The H-Bridge and Arduino should be commonly grounded. If two components that have ground terminals aren't connected, they will be "communicating" at different voltage levels and will most likely not work.
	  
#### <span style="color:red">Q. When I try to use pinMode() on the Tx and Rx pins, why does the wifi module does not work?</span> <a name ="q_wifi_pin"></a>

A. pinMode() is already done within the ENES100 library. Doing pinMode() on the pins again can cause conflicts within the code.

<!--### Sub paragraph <a name="subparagraph1"></a>
<>This is a sub paragraph, formatted in heading 3 style

<>## Another paragraph <a name="paragraph2"></a>
<>The second paragraph text
-->












