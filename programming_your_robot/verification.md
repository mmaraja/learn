---
layout: default
title:  "Verification"
permalink: verification
---

#### &uarr;[top](https://ubiquityrobotics.github.io/learn/)

## Basic Sanity Tests To Verify Core Operation

This page tells how to verify basic operation of the Magni robot.
If you have Magni Silver then the robot will have a few blue LEDs on top that will be helpful but not required to do these tests.

### Wifi HotSpot Verification

If no LAN cable is attached and if the robot has not been configured to look for a WiFi OR if no WiFi can be seen then the Magni software will create a HotSpot that you can connect to with your laptop.

If you have Magni Silver with the Sonar board then as the robot is powered up the LED2 (right LED on Sonar board as seen from the front) will light with dim blue light.
After 6 or so seconds that LED will turn off. After about 16 or so seconds if WiFi is able to come up LED2 will start to blink brightly about once per second indicating that the WiFi HotSpot is up.
At this time if you have on your smartphone some sort of WiFi network scanner you will see a ubiquityrobotics WiFi with last 4 digits being a unique hex value.

You will also see this HotSpot show up on your laptop and will be able to connect.  Read [HERE](https://learn.ubiquityrobotics.com/connecting) for more.

### Basic Movement Tests:
   - The first Test is a Firmware Only test: With no WiFi connected, have the red 'ESTOP' switch in the 'out' position and the black main power switch pushed in so the Magni is totally off. Then push the black main power switch which will turn on main power. At this point the main power switch will be in the 'out' position.

     RESULT: No jump in motors and motors are in the locked state strongly resisting movement.

<!--
   - The next tests are full system. Power up unit with ESTOP switch allowing power to motors AND/OR
ESTOP powering down motors then power up motors within 5 seconds.

 RESULT: Wheels PID locked wheels to a stopped state with full
resistance. (we did this in 5 sec to do so before motor node started up) -->
   - Edit ROS log with  
   ``vi `roslaunch-logs`/rosout.log``  

   and verify that the last
'Firmware version' line in the log is the expected firmware version.
   - Wait for motor node to be fully started which takes 20 seconds or
so sometimes.

     RESULT: Wheels PID locked still and no jump in movements.

<!--     
   - Using twist set to speed of 0.5 meters per second do following:
(rosrun teleop_twist_keyboard teleop_twist_keyboard.py)
     - Do a rostopic echo /odom to a window. Verify Position X is 0.
Use the 'i' key to rotate wheels very nearly 1 full as you can get
       RESULT: Position x: will be very near 0.64 meters
     - Using a stopwatch and Magni on blocks so it does not drive press
and hold 'i' which tries to move at 1 meter/sec.
       RESULT: 10 full turns should take 13 seconds if the Magni wheels
are running at 1 meter per second
-->
### Distance and Low Speed Movement Tests:

Enter keyboard movement using:

    rosrun teleop_twist_keyboard teleop_twist_keyboard.py  

  Press the  'z' key 12 times until the 'speed' value shows about 0.14 meters per second;
    the 'turn' value will show about 0.25

  At this point the robot will not move because when teleop is first entered it is in same state as if the 'k' was hit.

We need a second window open that we will call the 'tf' window; in that window type:

Also as setup have a second window open and in that type:

    rosrun tf tf_echo odom base_link  

This command will continually update the robot position. There will be one line that shows the translation and 3 values that are for X,Y,Z in meters.  The line will look like this:

    Translation: [0.000, 0.000, 0.100]   at first where X and Y are 0.000.

Now we will do a few tests and make sure the robot can move forward 1 meter and could have room to rotate fully.

   - In the teleop window press  the **i** letter at a quick rate for 4 seconds.   This should move the Magni about 0.6 meters forward.

   - Look at the 'Translation' line in the second tf window and the first of the 3 numbers is X and should be near 0.6 meters.

   - In the teleop window press the ',' (comma) key at a quick rate for 4 seconds. This should move the Magni about 0.6 meters straight back to where it started.

   - Look at the ‘Translation’ line in the second tf window and the first of the 3 numbers is X and should have returned to near 0.0.

   - Next press the j key at a quick rate for 6 seconds so the Magni rotates 90 degrees to face left. The tf window will have the 3rd line that says 'degrees' where at this rotation the 3rd number should be near 90 for 90 degrees to the left. If it goes too far you can use quick taps to the l key to inch it back to about 90.

   - Press the l letter key at a quick rate for 6 seconds and the Magni will rotate clockwise back to the starting point and will have the 3rd line that says 'degrees' now show the 3rd number to be near 0.

### RaspiCam Camera Test:

   There is a very simple way to test the RaspiCam camera on the robot.   This test will generate a jpeg still picture in about 6 seconds just to check the camera functionality.

   ``raspistill -o testpicture.jpg``

   To verify the camera is operating properly the testpicture.jpg file needs to be moved to your laptop or other computer that has a jpeg picture viewer.  If it is too difficult to move the picture using ftp or some other linux operation, the next best thing is to look at the file size. This can be done in the line below and the reply shows the 1543213 as the size in bytes for the jpeg image file.

   ``ls -l testpicture.jpg``

   ``-rw-rw-r-- 1 ubuntu ubuntu 1543213 Aug  4 08:18 testpicture.jpg``



### ESTOP Testing:
(Assumes rev 5.0 or later board. If not exception will be
noted)
   - For any rev 5.0 board and current code the ROS topic
/motor_power_active indicates if power is on or off.
     The topic is not instantaneous in response and can take a couple
seconds.  Note that ESTOP active means motor power off.
   - Press in to engage ESTOP with 0 cmd_vel OR non-zero cmd_vel. (keep
ESTOP pressed)

     RESULT: Wheels have slight resistance when ESTOP is active
   - Release ESTOP having not moved the motors and motor node is on

     RESULT: Wheels return to locked, stopped state with none or only a
tiny amount of movement noticed
   - Press ESTOP again and this time move the wheel a half revolution
(there is resistance but it moves).  Release ESTOP

     RESULT: For rev 5.0 board no wheel 'lurch' happens and motors
return to locked state with tiny or no movement.
     RESULT: For rev 4.9 board the wheel will snap back quickly the
half turn then lock. This cannot be avoided.

   - Run the joystick or use 'twist' to make motors actively move
(perhaps on blocks not on ground).  Press ESTOP to active state

     RESULT: Motors will no longer have power and will slow to a stop
with mild 'self braking' resistance to movement.
   - Continue to run the joystick for a couple seconds then release the
joystick and then release ESTOP a second later to power motors.

     RESULT: Rev 5.0 board will power up the wheels and there will be
tiny or no movement as wheels return to locked stopped state

     RESULT: This test is not recommended for rev 4.9 boards as ESTOP
switch could not be read so large movements can happen.
   - As before run the joystick with motors running then hold joystick
active and press ESTOP (wheels stop).  Release ESTOP in 3 sec

     RESULT: Even though joystick was active all the time, on release of
ESTOP after a half second or so wheels nicely ramp to speed again.

### Max Speed Limit Test:

Here we look to verify the max speed limit value will cause the robot to not exceed the default 1 meter per second setting.  We will again use teleop_twist_keyboard so just keep it active OR start it like this if not running yet

``rosrun teleop_twist_keyboard teleop_twist_keyboard.py``  

Place the robot on 'blocks' for the front wheel so the drive wheels do not touch the floor. Normally we put a block of wood or a small stack of books under the front of the robot and it raises it up so the wheels do not touch the floor. Put a piece of tape on the outside of a wheel so while testing we can count revolutions to get the actual speed.

YOU MUST HAVE THE ROBOT DRIVE WHEELS ELEVATED TO NOT TOUCH THE GROUND FOR THIS TEST!

In the teleop window press the **k** key once to be in "stop" mode then press the **z** key several times until the "speed" value shows a value just under 1.0 meters per second.  If you go too far the **,** (comma) key backs the speed value down.

In the teleop window press the **i** key repeatedly at a fast rate (3 or 4 times a second) and the wheels spin.

Verify the speed is going at 1 meter per second by watching the wheels turn 10 times in about 14 seconds.  The wheels have a circumference of just near 0.64 meters.  This is not a scientific test, it is looking for things being far off of the expected speed.

### Deadman Timer Testing:

The robot is designed to return to zero speed if
it loses touch with constant host velocity commands.
   - Startup and run the robot on blocks at a constant speed. Kill
the motor node OR disconnect serial (if your system allows).
     The motor node can be stopped and starting using:

     `sudo systemctl stop magni-base.service`

RESULT: The robot will return to stopped state with wheels actively locked.
   - Start or re-connect serial to the motor node using

   `sudo systemctl start  magni-base.service`

RESULT: Robot should be operational after the motor node starts (takes 15 or more seconds to start).

For re-connect of serial it will start back up in a second or less.
