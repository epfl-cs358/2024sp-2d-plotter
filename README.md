# MIT-2DPlotter

# Software:

Read the documentation on [https://github.com/gnea/grbl/wiki](https://github.com/gnea/grbl/wiki) for a better understanding. Feel free to also use ChatGPT to clear up any unclear sections regarding GRBL, wiring, etc.

## Arduino IDE and GRBL Installation

### Arduino IDE Installation

Install the latest Arduino IDE (2.x) from the official Arduino website: [https://www.arduino.cc/en/software](https://www.arduino.cc/en/software)

### GRBL (Modified version) Installation

Install and import the modified GRBL library (from [https://github.com/ruizivo/GRBL-28byj-48-Servo/tree/1d0d05261973064152ef94c29ec2d0c7e0b3aace](https://github.com/ruizivo/GRBL-28byj-48-Servo/tree/1d0d05261973064152ef94c29ec2d0c7e0b3aace)) using the following docs as a guide on how to import it into Arduino 2.x: [https://github.com/gnea/grbl/wiki/Compiling-Grbl#if-you-are-using-arduino-ide-2x](https://github.com/gnea/grbl/wiki/Compiling-Grbl#if-you-are-using-arduino-ide-2x)


---
💡 Note: Here, instead of download the GRBL source code, download the modified GRBL from here [https://github.com/ruizivo/GRBL-28byj-48-Servo/tree/1d0d05261973064152ef94c29ec2d0c7e0b3aace](https://github.com/ruizivo/GRBL-28byj-48-Servo/tree/1d0d05261973064152ef94c29ec2d0c7e0b3aace).

We must use the `June 12, 2016` commit version (as the latest version has compilation errors).

We use the modified GRBL library as it supports the BYJ48 stepper motors directly.
---

Once the Arduino board is correctly selected and you run `grblUpload`, go to Tools>Serial Monitor and select `115200 baud` (re-run if needed). 

You should then see a message such as in the serial monitor: `Grbl 0.9j ['$' for help]`

This indicates that the grbl was successfully installed!

## Wiring

You can read about GRBL wiring on the following docs: [https://github.com/gnea/grbl/wiki/Connecting-Grbl#grbls-pins](https://github.com/gnea/grbl/wiki/Connecting-Grbl#grbls-pins). We use the modified GRBL, which already defines which pins to connect our stepper motor drivers and servo to, as defined in the table below.

![Circuit Diagram.png]()

Wiring Configuration Diagram

First, connect the stepper motors to the stepper motor drivers (there is only one possible direction to plug in).

Then, connect devices as follows:

| Device | Pin |
| --- | --- |
| Servo (Yellow Wire) | D11 |
| X-Axis Stepper Driver (IN1-IN4) | Pins D2-D5 |
| Y-Axis Stepper Driver (IN1-IN4) | Pins A0-A3 |

We will also include limit switches. Limit switches allow our CNC machine to emergency stop when we reach the end our our axle. There are two configuration options, NC (normally closed) and NO (normally open). We go with the typical NC, which is safer as if there is a wire problem then the circuit will be open (thus will send an alarm signal).

![Untitled](2D%20Plotter%20166d9bd0cc4e49f894fa3b567d57807f/Untitled.png)

| Device | NC (lever sticking up end) | COM (lever down end) |
| --- | --- | --- |
| X-Axis Limit | D9 | GND |
| Y-Axis Limit | D10 | GND |

Finally, connect the power and ground cables as shown in the Wiring Configuration diagram above (the steppers and servos are also correctly shown):

## Universal GCode Sender

Use the Universal Gcode sender application, to connect and control your CNC machine (this can also be done via the Serial Monitor in your Arduino IDE). You can also use it to send a gcode image to your hardware device: https://github.com/winder/Universal-G-Code-Sender

Download and Install the latest Platform version.

<aside>
💡 Note on MacOS, after double-clicking the .dmg file you will not get any pop-up/installation tips. Instead, check your finder sidebar, and you will see the mounted disk “Universal G-Code Platform”. Click here, then drag and drop the “Universal G-Code Platform” into your applications folder and then launch.

![Untitled](2D%20Plotter%20166d9bd0cc4e49f894fa3b567d57807f/Untitled%201.png)

</aside>

Then, connect to your device by specifying as below (and the correct port)

![Untitled](2D%20Plotter%20166d9bd0cc4e49f894fa3b567d57807f/Untitled%202.png)

Once connected, you should see something like this:

![Untitled](2D%20Plotter%20166d9bd0cc4e49f894fa3b567d57807f/Untitled%203.png)

On the bottom left, you can use the jog controller to control your axis. If everything is wired correctly, then you can use this to move your Y and X axis controllers manually.

Since homing does not work, you can use this to manually “reset” your CNC to the start position before you start drawing.

## Configuration

### Servo configuration

With everything wired up correctly and GRBL running on your Arduino, we will now set-up the servo (so that it can move the pen up and down).

To control the servo through the GRBL (/Arduino Serial monitor Terminal), you can send the following commands:

- Command `M03 Sxxx` (xxx between 0 and 255) to rotate the servo between 0-90.
- Command `M05` to turn the servo to zero degrees.

If none of these work commands work, make sure to double-check your wiring to see that the servo works. In the worst case, trying running an Arduino example file for controlling the servo to verify it works correctly.

For our set-up, do as follows:

- Set the servo to degree 0 using `M05` or `M03 Sxxx`. Then, connect the white servo lever, pointed upwards at 45 degree angle
    
    ![IMG_0272.HEIC](2D%20Plotter%20166d9bd0cc4e49f894fa3b567d57807f/IMG_0272.heic)
    

- Now, when you move to degree 85 (`M03 S225`**)**, it should go downwards allowing the pen to touch the paper
    
    ![IMG_0273.HEIC](2D%20Plotter%20166d9bd0cc4e49f894fa3b567d57807f/IMG_0273.heic)
    

## Set-up Limit Switches (Optional)

We have included limit switches so that it performs a hard-stop when your CNC machine is about to hit the corner. Make sure you have wired your limit switches as explained above.

Then, within Arduino Serial Monitor or UGS Platform Console (connecting to your Arduino running GRBL), you can send the following command:

Type `$` into the terminal to the connected 2D Plotter

![Untitled](2D%20Plotter%20166d9bd0cc4e49f894fa3b567d57807f/Untitled%204.png)

`$21=1` → Enable hard limits (so the CNC machine stops if hitting a switch)

`$$` → To verify it is correctly saved

If wired correctly, you should see the following error when hitting a limit switch in Universal G-Code Sender

![Untitled](2D%20Plotter%20166d9bd0cc4e49f894fa3b567d57807f/Untitled%205.png)

![Untitled](2D%20Plotter%20166d9bd0cc4e49f894fa3b567d57807f/Untitled%206.png)

If you get an alarm, then it means a limit switch was triggered. To clear the alarm:

1. Use command `$X` (sometimes you’ll have to disconnect then reconnect, then send the `$X` command).
2. Then, jog the controller so that the the limit switch is released (i.e. move away from the axis.
3. Repeat steps 1 & 2 if the alarm triggered again when the switch was released.

## Set-up Homing (Not Working)

Once the limit switches are set-up, homing can be done (so that the CNC machine resets to initial conditions).

`$22=1` → Enable homing

`$23=1` → Set the X-axis and Y-axis homing directions

`$$` → Verify the configuration

`$H` → You can then use this command to home the device to the limit switches

(In case there are alarms, you can use the `$X` command to clear them (after verifying your CNC is not stuck/obstructed by something)

❗We had troubles to set-up the homing, which could be because of limit switch interference. In order to mitigate this, we need wire shielding (from the motors) and noise filtering (resistors and capacitors for the limit switches). See following: [https://github.com/winder/Universal-G-Code-Sender/issues/1208#issuecomment-482788731](https://github.com/winder/Universal-G-Code-Sender/issues/1208#issuecomment-482788731)

## Creating Gcode from PNG

Follow instructions from here: [https://youtu.be/d8ZynbxfBDo?feature=shared&t=658](https://youtu.be/d8ZynbxfBDo?feature=shared&t=658)

Download and install Inkscape v0.48.5 ([https://inkscape.org/release/inkscape-0.48.5/windows/32-bit/exe/dl/](https://inkscape.org/release/inkscape-0.48.5/windows/32-bit/exe/dl/)). Newer versions don’t support the plugin that we use to convert the image to gcode (and control the servo).

<aside>
❗ A Windows computer is required for this specific version of InkScape (as the latest MacOS doesn’t support this old version of InkScape).

</aside>

Then install and add the extension to InkScape (from the video). We need this extension as it tells UGS when to bring the pen up and down. Continue the video tutorial for generating your own Gcode.

You can go check the “/Software” folder of this Github Repository to see some of the gcode files we generated (includes a square, a cat, a smaller cat and a “Making Intelligent Things” text.

Once you have wired everything correctly, verified via the GCode Platform jog controller that the steppers and servo are working correctly, then you are ready to draw some images!

In Universal Gcode Platform:

1. Use the jog controller to bring your pen to the starting corner (test and identify where this is)
2. File>Open a Gcode file
3. Click on “Send” (or Program>Send) and watch the machine print!

Trouble shoot as necessary afterwards (e.g. re-adjusting belt, …).

