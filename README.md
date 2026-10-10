# Arduino & Python Laptop Joystick 🕹️💻

Transform an analog joystick connected to an Arduino into a physical mouse cursor controller for your computer! This project bridges hardware and software by reading joystick coordinates on an Arduino microcontroller and streaming them via Serial communication to a Python script that controls the laptop's cursor using `pyautogui`.

---

## 🚀 Project Overview

The project consists of two core components:
1. **The Arduino Sketch (`.ino`):** Reads analog values from the joystick axes (`X` and `Y`) and digital state from the button (`pres_pin`), centers the raw sensor readings, and transmits them over a Serial connection at `115200` baud.
2. **The Python Script (`.py`):** Listens to the specified serial port (`COM4`), parses the incoming `X` and `Y` offset coordinates, and moves the laptop cursor dynamically across the screen with built-in auto-reconnection logic.

---

## 🛠️ Tech Stack & Dependencies

### Hardware
* **Arduino Board** (Uno, Nano, or compatible)
* **Analog Joystick Module** (with X, Y axes and a push-button)
* **Jumper Wires**
* **USB Cable** to connect Arduino to the PC

### Software & Libraries
* **Arduino IDE** (for flashing the microcontroller)
* **Python 3.x** with the following packages:
  * `pyserial` (for reading serial data)
  * `pyautogui` (for controlling mouse movement)
  * `schedule` (for task scheduling)

Install the required Python packages via terminal:
```bash
pip install pyserial pyautogui schedule
```

---

## 🔌 Wiring Diagram

Connect your joystick to the Arduino board as follows:

| Joystick Pin | Arduino Pin | Description |
| :--- | :--- | :--- |
| **VRX (X-Axis)** | `A0` | Analog input for horizontal movement |
| **VRY (Y-Axis)** | `A1` | Analog input for vertical movement |
| **SW (Button)** | `Pin 9` | Digital input (configured with `INPUT_PULLUP`) |
| **VCC** | `5V` | Power supply |
| **GND** | `GND` | Ground |

---

## 📝 Code Breakdown

### 1. Arduino Code (Microcontroller Side)
* **Pin Configuration:** Sets up `A0` and `A1` as analog inputs and `Pin 9` with an internal pull-up resistor.
* **Centering Logic:** Arduino analog reads range from $0$ to $1023$ (centered around $512$). The script subtracts the center offsets (`511` for X, `507` for Y) to produce positive and negative relative displacement values.
* **Threshold Filter:** Deadzones filter out minor noise values ($< 10$).
* **Data Format:** Packages the data as `X_value` + `"x"` + `Y_value` terminated by a newline (`\n`).

### 2. Python Code (Host Side)
* **Serial Listening:** Opens communication on port `COM4` at `115200` baud. *(Note: Update the `PORT` variable to match your system)*.
* **Data Parsing:** Reads the incoming serial stream, splits the string by the `'x'` separator, and converts values to floats.
* **Cursor Scaling:** Divides the incoming displacement coordinates by `15` to ensure smooth, controlled cursor movement. The Y-axis is inverted (`* (-1)`) to match standard screen coordinates.
* **Auto-Reconnection:** Includes robust exception handling to safely close and reopen the serial connection if the Arduino is unplugged or resets.

---

## ⚙️ Getting Started & Usage

1. **Flash the Arduino:** Open the Arduino IDE, paste the C++ code, select your correct Board and Port, and upload it to your Arduino.
2. **Configure Python Script:** Open the Python script and verify that the `PORT` variable matches your Arduino's active port (e.g., `'COM3'` or `'COM4'`).
3. **Run the Script:**
   ```bash
   python joystick_cursor.py
   ```
4. **Control:** Move the physical joystick to steer your laptop cursor. Press `Ctrl + C` in the terminal to stop the program safely.

---


