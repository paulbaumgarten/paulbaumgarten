# Cozmo Robotics

## Summary

Learn to program the Anki Cozmo robot with Python!

**Note: It is highly recommended to have a printed or saved copy of this tutorial available before you start. Using the Cozmo requires you to connect to it over wifi, meaning your computer will no longer have access to the internet through your usual network connection.**

Students of mine will be given this as a print out. You should check my website for any updates/changes @ [https://pbaumgarten.com/myp-design/cozmo/](https://pbaumgarten.com/myp-design/cozmo/)

## Unit information

* Statement of inquiry: Robotic systems are a technical innovation where computers are capable of functioning in and adapting to a variety of physical environments.
* Key concept: Systems
* Related concepts: Function, Adaptation
* Global context: Scientific and technical innovation
* Assessment objectives: A,B,C,D

## Lesson overviews

12 lessons as follows:

* Lesson 1. Connect to Cozmo
* Lesson 2. Basic movement
* Lesson 3. Detect cliff edge, drive around a tabletop
* Lesson 4. Take photo, save photo
* Lesson 5. Detect ArUcode markers
* Lesson 6. Take action based on ArUcode markers
* Lesson 7. Design your own obstacle course
* Lesson 8,9,10. Build your own obstacle course
* Lesson 11. Evaluate your own obstacle course

# 1. Connect to Cozmo

<img src="assets/cozmo-screen-code.png" width="30%" style="float:right">

* Install `Python` if you haven't already. It can be downloaded from [python.org](https://python.org/).
* Install the required cozmo packages. From Windows command prompt this is `pip install pycozmo cozmo_wrapper`
* Sit the Cozmo on it's charger unit which must be powered on (it doesn't have to be connected to your computer, just usb power).
* On the front screen, Cozmo will display a code similar to the one displayed. 
* Open your computer network settings and search for the wifi connection with Cozmo's name in it. Connect to the Cozmo wifi using the code on the Cozmo's front screen as the password.

<img src="assets/cozmo-connection.png" width="30%" style="float:right">

* Open `VS Code` or your preferred Python editor. The following is a simple program to test your connection is working properly. It will cause the backlight on the cozmo to turn green for 1 second, then red for 1 second, then off.

```python
# You will require these import statements for all Cozmo programs
import time
import math
import pycozmo
from cozmo_wrapper import Cozmo

# Connect to Cozmo
cozmo = Cozmo()
cozmo.start()
cozmo.connect()
cozmo.wait_for_robot()
# Do something simple
cozmo.set_all_backpack_lights(pycozmo.lights.green_light)
time.sleep(1)
cozmo.set_all_backpack_lights(pycozmo.lights.red_light)
time.sleep(1)
cozmo.set_all_backpack_lights(pycozmo.lights.off_light)
# Disconnect from Cozmo
cozmo.disconnect()
cozmo.stop()
```

## Your task/s

* Install Python and VS Code if you don't already have it
* Install the `cozmo_wrapper` and `pycozmo` libraries
* Connect to your Cozmo over wifi
* Get the sample test code above working to prove you can connect and control your Cozmo
* Start experimenting with other code

# 2. Basic movement

The following demonstrate some of the commands you can use to control different features of the Cozmo.

LED commands

```python
# The multicolor LED on the back of the Cozmo
cozmo.set_all_backpack_lights(pycozmo.lights.red_light)
cozmo.set_all_backpack_lights(pycozmo.lights.green_light)
cozmo.set_all_backpack_lights(pycozmo.lights.blue_light)
cozmo.set_all_backpack_lights(pycozmo.lights.white_light)
cozmo.set_all_backpack_lights(pycozmo.lights.off_light)

# Front facing head light
cozmo.set_head_light(True)    # Turn on
cozmo.set_head_light(False)   # Turn off
```

Driving commands

```python
# Drive forward at speed 50
cozmo.drive_wheels(lwheel_speed=50.0, rwheel_speed=50.0)

# Drive forward at speed 50 for 5 seconds then stop
cozmo.drive_wheels(lwheel_speed=50.0, rwheel_speed=50.0, duration=5.0)

# Drive the right wheel (making the robot veer left)
# for 2.5 seconds
cozmo.drive_wheels(rwheel_speed=50.0, duration=2.5)

# Drive the left wheel forward, right wheel in reverse
# (making the robot veer right) ... notice the minus sign!
cozmo.drive_wheels(lwheel_forward=50.0, rwheel_speed=-50.0)

# Drive in reverse at speed 50 (notice the minus signs!)
cozmo.drive_wheels(lwheel_speed=-50.0, rwheel_speed=-50.0)

# All stop
cozmo.stop_all_motors()
```

Tilt head up/down

```python
# === Move Cozmo's head ===
# Cozmo's head can typically move in a range from -25 degrees 
# (looking down) to +44 degrees (up). Python expects the angle
# to be in radians so it needs converting from degrees.
# The angle can be provided as a variable or a number.
angle = 0
cozmo.set_head_angle(radians(angle)) 
```

Raise/lower lifting arm

```python
# === Move Cozmo's lifting arm ===
# Cozmo's lifting arm can move in a range from 
# 32mm (fully lowered) to 92mm (fully raised)
# Height can be provided as a variable or a number.
height = 90
cozmo.set_lift_height(height)
```

## Your task/s

Using a combination of the above commands, can you make your Cozmo...

* Drive in a perfect square of sides 1 meter in length, and sharp 90 degree turns? It should stop at the spot it started, facing the same way when done.
* Go in a perfect circle (diameter at least 50cm), stopping and starting at the same point.

# 3. Detect cliff edge, 

Cozmo has sensors built in through which it can detect different situations and events. The following command `add_handler` tells Cozmo that if a particular event occurs, there is a function we have written to handle it. In this case, the event is if a cliff edge is detected, and the handling function is specified in the 2nd parameter as being called `on_cliff_detected`.

```python
cozmo.add_handler(pycozmo.event.EvtCliffDetectedChange, on_cliff_detected)
```

So if our main section is going to tell Cozmo to run the `on_cliff_detected` function, we need to ensure it actually exists. Here is an example of what it could look like. You can modify yours to do something different.

```python
def on_cliff_detected(client, state):
    global cozmo    # Bring the cozmo object into this function
    if state:
        print("Cliff detected.")
        # Reverse straight back
        cozmo.drive_wheels(lwheel_speed=-50.0, rwheel_speed=-50.0, duration=1.0)
        # Reversing turn
        cozmo.drive_wheels(lwheel_speed=0.0, rwheel_speed=-50.0, duration=2.8)
        # Drive forward
        cozmo.drive_wheels(lwheel_speed=50.0, rwheel_speed=50.0)
```

There are other handlers you should know about. You don't need them for this lesson, but they may come in handy for a later lesson so I'll include the notes on them now just in case.

Detect pressing the button on top of Cozmo...

```python
# This handler will trigger when the button on top of Cozmo is pressed
cozmo.conn.add_handler(pycozmo.protocol_encoder.ButtonPressed, on_button_pressed)

# Example handler function for button press
def on_button_pressed(cli2, pkt):
    global cozmo
    if pkt.pressed:
        print("Button pressed.")
        cli.drive_wheels(lwheel_speed=50.0, rwheel_speed=50.0)
```

Detect when Cozmo is picked up...

```python
# This handler will trigger when the robot is picked up
cozmo.add_handler(pycozmo.event.EvtRobotPickedUpChange, on_robot_picked_up)

# Example handler function for picked up
def on_robot_picked_up(cli, state):
    global cozmo
    if state: ## state is set to True if picked up
        print("Picked up.")
        cozmo.stop_all_motors()
```

## Your task/s

* a) Can you make a Cozmo drive around a table top without falling off?
* b) Make the colour of the light on Cozmo change to indicate if it is going forward, turning, or in reverse.
* c) Make it so Cozmo keeps driving around the table top until you press the button on the top.

# 4. Take photo, save photo

Your Cozmo has a camera. Admitted the resolution isn't great, but you can see what it sees easily enough. After we enable the camera, we then create another event handler to process the content of the image we receive.

```python
cozmo.conn.send(pycozmo.protocol_encoder.EnableCamera(enable=True))
cozmo.conn.send(pycozmo.protocol_encoder.EnableColorImages(enable=True))
cozmo.add_handler(pycozmo.event.EvtNewRawCameraImage, process_photo, one_shot=True)
```

An example event handling function could look like...

```python
def process_photo(cli, image):
    del cli
    # saves the image to your project folder
    image.save("camera.png", "PNG")
    # shows the image on your laptop screen
    image.show()
```

## Your task/s

* Modify your table-top driving program so every time it reaches a cliff, the Cozmo takes a photo of the wonderful view. Upload your code and an example photo to your portfolio.

# 5. Detect ArUco markers

While the view from the Cozmo camera might be nice, it also has a practical purpose. We can use vision recognition systems to recognise what the Cozmo can see and behave accordingly.

<img src="arucodes/4x4_1000-50-small.png" style="float:right">

For this exercise we are going to keep it simple and use some ArUco Markers. Think of ArUco as mini-QR-codes. They are a 2D black and white block code that are very easy for the vision system to recognise and decode. There are a couple of different versions, but the one we are using will create 4x4 blakc and white grids that Python will recognise as being a number between 0 and 999. This ArUco represents the number 50. 

You will need to print your own ArUco labels. You can generate them from this website [http://chev.me/arucogen/](http://chev.me/arucogen/) (keep the "dictionary" setting on 4x4). From my trials a marker of 50mm was detectable by the Cozmo at a distance of approximately 50cm. You will have to experiment with the sizes you need based on the range you want it to recognise the code.

```python
def saw_marker(cozmo, markers):
    # The callback function receives two parameters: the cozmo client object, and a list of integers of markers seen
    if 70 in markers:
        print("I saw ArUco marker 70")
    if 71 in markers:
        print("I saw ArUco marker 71")
    if 72 in markers:
        print("I saw ArUco marker 72")
        cozmo.alive = False   # Tell our Cozmo program it's time to end

coz = Cozmo()
coz.start()
coz.connect()
coz.wait_for_robot()
# Look straight ahead
coz.set_head_angle(0.0)
# When you see an ArUco, run the `saw_marker` function
coz.on_aruco(saw_marker)    
while coz.alive:
    time.sleep(0.1)
coz.disconnect()
coz.stop()
```

## Your task/s

* Print a few different ArUco markers of different sizes. Experiment with under what circumstances Cozmo can read them (angle, lighting, what if something partially obscures the code?)

# 6. Take action based on ArUcode markers

Make a new copy of your table-top driving code. We're going to experiment with using Cozmo to detect ArUco markers that change it's behaviour.

* a) Start simple: make Cozmo start the table-top program when shown one ArUco marker, and stop when it sees a different ArUco marker.
* b) Attach an ArUco to an object. Have the Cozmo detect the object and drive around it.
* c) Attach ArUco markers to two objects, one to trigger a turn left behaviour, the other to trigger a turn right behaviour.
* d) What else can you think of to try with the markers?

# 7. Design your own obstacle course

By now you have experimented with quite a few features of the Cozmo. Design an obstacle course that you will build for the Cozmo to self navigate from point A to point B.

## Your task/s

Create planning drawings for your obstacle course and include those in your portfolio for lesson 7.

# 8,9,10. Build your own obstacle course

Create your obstacle course (1 lesson) and program your Cozmo to complete it (2 lessons).

How long does it take your cozmo to capture the flag?

## Your task/s

* a) Submit a photo of your final obstacle course into your portfolio.
* b) Submit your Python code into your portfolio.

# 11. Evaluate your own obstacle course

## Your task/s

Make a video recording demonstrating the success (complete or partial) of your Cozmo robot...

* a) Introduce the obstacle course, outline the different features and what you wanted the Cozmo to do at each.
* b) Show the Cozmo attempting your obstacle course
* c) Discuss your level of success. What parts worked well? what did not? What changes would you make if given more time?

Upload your video to your portfolio.

# Resources & references

The PyCozmo module website is [https://github.com/zayfod/pycozmo](https://github.com/zayfod/pycozmo)

To install packages from IDLE, use the following two lines....

```python
from pip._internal import main as pipmain
pipmain(['install', 'pycozmo', 'cozmo_wrapper'])
```
