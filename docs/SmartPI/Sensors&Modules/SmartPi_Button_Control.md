# Button Message Display with Smart Pi One, SmartPi-GPIO, and Pull-down Resistor (3.3V)

In this guide, we will demonstrate how to display a message when a button connected to the **Smart Pi One** is pressed, using the **SmartPi-GPIO** library and a **pull-down resistor** connected to the **3.3V** pin.

![Smart Pi One - Button](../../../img/SmartPi/Sensors&Modules/SmartPi_Button_Control/SmartPi_Button_Control_1.png)


We will cover the following methods:
- **CLI commands**
- **Python script**
- **C program**

## Required Materials

- Smart Pi One
- button (with resistor 10kΩ if necessary)
- Connecting wires
- Breadboard (optional for easier connections)

### Wiring Diagram

The button is connected to **GPIOG11 (Pin 7)** as the input pin for detecting the button press. A **10kΩ pull-down resistor** is placed between **GPIOG11** and **Ground (Pin 7)**. This setup ensures the pin reads **LOW** when the button is not pressed and **HIGH** when the button is pressed due to the connection to **3.3.3V (Pin 1)**.

| **Pin Number** | **Pin Name**          | **Function**           |
|----------------|-----------------------|------------------------|
| 1              | 3.3V                  | Power Supply            |
| 7            | GPIOG11                | Button Input            |
| 7             | GND                   | Ground                  |

<img src="../../../img/SmartPi/Sensors&Modules/SmartPi_Button_Control/SmartPi_Button_Control_2.png" width="520" alt="BUTTON Wiring Diagram">

## Displaying a Message via CLI

You can detect button presses using CLI and print a message accordingly.

### Steps:

1. **Configure the button pin as input**:
   ```bash
   sudo gpio 7 mode in pull-down
   ```

2. **Read the button state**:
   ```bash
   sudo gpio 7 read
   ```

3. **Example to display a message**:
   Use a loop to continuously check the button status and display a message when pressed:
   ```bash
   while true; do
     if [ $(sudo gpio 7 read) -eq 1 ]; then
       echo "Button Pressed!"
       break
     fi
     sleep 0.1
   done
   ```

This will print "Button Pressed!" when the button is pressed.

## Using Python

### Prerequisites: Configuration of smartpi-gpio

To install **SmartPi-GPIO** on your Smart Pi One, follow these steps:

1. **Update system**:
   ```bash
   sudo apt update 
   sudo apt-get install -y python3-dev python3-pip libjpeg-dev zlib1g-dev libtiff-dev
   sudo mv /usr/lib/python3.11/EXTERNALLY-MANAGED /usr/lib/python3.11/EXTERNALLY-MANAGED.old

2. **Clone the repository**:
   ```bash
   git clone https://github.com/ADNroboticsfr/smartpi-gpio.git
   cd smartpi-gpio

3. **Install the library**:
   ```bash
   sudo python3 setup.py sdist bdist_wheel
   sudo pip3 install dist/smartpi_gpio-1.0.0-py3-none-any.whl


4. **Activate GPIO interfaces**:
   ```bash
   sudo activate_interfaces.sh
    pip install smartpi-gpio
    ```

## Displaying a Message with Python

With **SmartPi-GPIO** and Python, you can write a simple script to detect the button press and display a message.

### Steps:

1. **Create a Python file**:
   ```bash
   nano button_message.py
   ```

2. **Write the following code**:

   ```python
   from smartpi_gpio.gpio import GPIO
   import time

   # Initialize GPIO instance
   gpio = GPIO()

   # GPIO pin number for the button (GPIOA7)
   button_pin = 7

   # Configure the button pin as input with pull-down resistor
   gpio.set_direction(button_pin, "in", "pull-down")

   print("Press the button to display a message...")

   while True:
       # Read the button state
       button_state = gpio.read(button_pin)
       
       if button_state == '1':  # If button is pressed
           print("Button Pressed!")
           break

       time.sleep(0.1)
   ```

3. **Save and exit** (`CTRL+X`, `Y`, and `Enter`).

4. **Run the Python script**:
   ```bash
   python3 button_message.py
   ```

When the button is pressed, the message "Button Pressed!" will be displayed.

##  Displaying a Message with a C Program

You can also detect button presses using a C program and the **SmartPi-GPIO** library.

### Steps:

1. **Create a C file**:
   ```bash
   nano button_message.c
   ```

2. **Write the following code**:

   ```c
   #include <stdio.h>
   #include <stdlib.h>
   #include "smartpi_gpio.h" // Include SmartPi-GPIO header

   int main() {
       // Initialize the GPIO library
       if (gpio_init() == -1) {
           printf("Failed to initialize GPIO.\n");
           return -1;
       }

       int button_pin = 7; // GPIOA7 (Pin 12)

       // Set the button pin as input with pull-down resistor
       gpio_set_direction(button_pin, GPIO_INPUT_PULLDOWN);

       printf("Press the button to display a message...\n");

       while (1) {
           // Read the button state
           int button_state = gpio_read(button_pin);

           if (button_state == 1) {
               printf("Button Pressed!\n");
               break;
           }

           // Small delay to prevent busy-waiting
           usleep(100000); // 100 ms
       }

       // Cleanup and close GPIO
       gpio_cleanup();

       return 0;
   }
   ```

3. **Save and exit** (`CTRL+X`, `Y`, and `Enter`).

4. **Compile the C program**:
   ```bash
   gcc -o button_message button_message.c -lsmartpi_gpio
   ```

5. **Run the program**:
   ```bash
   sudo ./button_message
   ```

When the button is pressed, the message "Button Pressed!" will be displayed.
