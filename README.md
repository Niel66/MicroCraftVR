## What is it?

This project allows you to play minecraft in VR using a [BBC micro:bit](https://microbit.org/) as the game controller. To get the code to work, you'll need a couple of extra Python modules installed onto your local machine:
- [Pynput](https://pynput.readthedocs.io/en/latest/), "Monitor and control user input devices"
- [David Whale](https://github.com/whaleygeek)'s [bitio library](https://github.com/whaleygeek/bitio), which "allows you to run code in Python on a PC/Mac/Linux/Raspberry Pi and interact directly with the micro:bit"

## Installation
### Setting up your PC
You'll need to set up the device on which the game will be played first.
1. If you don't already have Python, [download Python 2.7 from this link](https://www.python.org/download/releases/2.7/)
2. If you don't already have Pip, [install it by following these instructions](https://pip.pypa.io/en/stable/installing/). Pip is a "package manager" for Python, and makes getting set up with Python packages really easy.
3. Get the MicroBike folder and install the required modules.

**Using command line**

Open a new [command line window](https://www.computerhope.com/jargon/c/commandi.htm). This is called 'Terminal' on a Mac, 'Command Prompt' on Windows, and 'shell' or 'terminal' on Linux. Type the following:

   ```git clone https://github.com/musabkilic/MicroBike```

This gets the latest MicroBike code from this [Git repository](https://help.github.com/articles/about-repositories/).

   Navigate to the MicroBike folder in your command line window using the ['cd' command](https://en.wikipedia.org/wiki/Cd_(command)) - you may need to change the path, depending on how you've configured git on your computer:

   ```cd MicroBike```

Next, install the required modules:

   ```pip install -U -q -r requirements.txt```

Click below to see a demonstration of this:

[![Installing Required Modules for MicroBike](https://github.com/musabkilic/MicroBike/raw/master/res/command_line.png)](https://www.youtube.com/watch?v=x_Vw__5VoTY "Installing Required Modules for MicroBike")

**Setting up the bitio library**

See [this](https://github.com/whaleygeek/bitio#getting-started) to set up [David Whale](https://github.com/whaleygeek)'s [bitio library](https://github.com/whaleygeek/bitio).

### Setting up your micro:bit
Connect your micro:bit to your computer. Get the latest [bitio.hex](https://github.com/whaleygeek/bitio/raw/master/bitio.hex) from the bitio repository, and [drag this hex file to your micro:bit to 'flash' it to the device](https://microbit.org/guide/hardware/usb/).

If you're on Windows, you'll also need to [install the Windows serial driver](https://os.mbed.com/docs/v5.9/tutorials/windows-serial-driver.html) on your computer.

**Done!** You can use MicroBike by typing ```python controller.py``` in your computer's command line.


## How does it work?
Let's review the code for [controller.py](https://github.com/musabkilic/MicroBike/blob/master/controller.py) to understand how this works.

```python
import microbit
import time
from pynput.keyboard import Key, Controller
```

We need to import the modules to use them later. We will use 3 modules; microbit module for controlling and reading data from the micro:bit, time module for waiting for a specific time step and pynput module to control the keyboard(and the game of course).

```python
#Function for Changing a Key
def changeKeyState(key, value):
	global keyboard_keys

	#Change Only Neccessary
	if value!=keyboard_keys.get(key, False):
		if value:
			keyboard.press(key)
		else:
			keyboard.release(key)

	keyboard_keys[key] = value
```

`changeKeyState` is a function, it will help us to control the keyboard keys - for example if the handlebar goes left, it will press the left arrow key.

```python
#Specify Keyboard
keyboard = Controller()
#Set Accelerometer Values
previous_values = microbit.accelerometer.get_values()
keyboard_keys = {}
#Set Images
stable = microbit.Image("00000:00000:99999:00000:00000")
images = {"N": microbit.Image.ARROW_N,
		  "NE": microbit.Image.ARROW_NE,
		  "NW": microbit.Image.ARROW_NW,
		  "E": microbit.Image.ARROW_E,
		  "W": microbit.Image.ARROW_W,
		  "": stable}
```

We will define some variables to use them later.

```python
#Wait for User to Press a Button
while 1:
	#Blink
	microbit.display.show(microbit.Image.ARROW_W)
	time.sleep(0.5)
	microbit.display.clear()

	#Start the Program if a Button is Pressed
	if microbit.button_a.was_pressed() or microbit.button_b.was_pressed():
		break
	time.sleep(0.5)
```

This is the first loop. It will keep blinking until the user presses the A or B button. After pressing the button controller will start running.

```python
#Start the Loop
while 1:
	#Get Accelerometer Values
	accelerometer_values = microbit.accelerometer.get_values()
	x,y,z = accelerometer_values

	#Calculate Avarege Motion in X,Y,Z Directions
	motion = sum(map(lambda x:abs(accelerometer_values[x]-previous_values[x]),range(3)))/3
```

This is the main loop. We will start by getting required values and calculating the motion.

```python
	#Change Direction
	changeKeyState(Key.up, y>400)
	changeKeyState(Key.right, x>60)
	changeKeyState(Key.left, x<-60)
	changeKeyState(Key.shift, motion>500)

	#Set Direction to Show
	direction = ""
	if y>400:
		direction += "N"
	if x>60:
		direction += "E"
	elif x<-60:
		direction += "W"

	#Show the Direction
	microbit.display.show(images[direction])
	#Set Current Accelerometer Values to Previous
  previous_values = accelerometer_values
```
Then we will use the information we get before to control the game. Keyboard keys will trigger when the microbit turns right or left higher than a specific value.

We will use same information for changing the direction on the microbit.

<p align="center">
	Musab Kılıç<br/>
	@musabkilic & @musabkilic0<br/>
	<a href="https://github.com/musabkilic/MicroBike/blob/master/LICENSE">Licensed under the GNU General Public License v3.0</a>
</p>
