# People_Tracking_Camera_with_servo

This project implements an automatic face‑tracking camera using a Raspberry Pi, a standard USB camera (or CSI camera), and a continuous rotation servo. The system detects a face with OpenCV’s Haar cascade, computes the horizontal offset from the center of the frame, and drives the servo proportionally to keep the face centered.

The control algorithm is a simple proportional speed controller. When no face is detected, the servo stops after a short timeout and optionally returns to a neutral position. The code is written in Python and uses the pigpio library for precise servo pulse‑width control.
Features

Real‑time face detection with Haar cascades

Continuous rotation servo control (speed & direction)

Proportional speed regulation with adjustable gain and dead zone

Automatic stop when no face is detected

Low CPU usage (detection runs on downscaled frames)

Easily configurable parameters

Hardware Requirements

Raspberry Pi (3B+, 4B, or Zero 2 W recommended)

Camera: any camera supported by OpenCV (USB or CSI). Tested with C‑TECH CAM‑07HD.

Continuous rotation servo (e.g., MG90S, FS90R)

External 5V power supply for the servo (RPi’s 5V pin is not sufficient under load)

Jumper wires, breadboard (optional)

Resistors: none required if using a continuous servo directly (most work with 3.3V logic)

Wiring
Servo Wire	Raspberry Pi GPIO
VCC (red)	External 5V power (common ground with Pi)
GND (brown)	GND (pin 6) – also connected to external supply ground
Signal (orange)	GPIO 18 (pin 12)

Important: Use a separate 5V power supply (e.g., a 5V UBEC or a battery pack) capable of at least 1A. Connect the ground of that supply to a GND pin on the Pi to establish a common reference.
#Software Installation

Prepare your Raspberry Pi
Install Raspberry Pi OS (64‑bit recommended) and update:

`
sudo apt update && sudo apt upgrade -y
`
Enable the camera and GPIO


sudo raspi-config

Interface Options → Camera → Enable

(Optional) Interface Options → SPI/I2C → Enable

Install system dependencies
bash

sudo apt install python3 python3-pip python3-venv git pigpio

Start the pigpio daemon
bash

sudo systemctl enable pigpiod
sudo systemctl start pigpiod

Clone this repository
bash

git clone https://github.com/Sans-coding-the-skeleton/People_Tracking_Camera_with_servo.git
cd people-tracking-camera

Create a virtual environment and install Python packages
bash

python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt

The requirements.txt should contain:
text

opencv-python
pigpio
numpy

Calibration: Finding the Servo Stop Pulse

Continuous rotation servos have a “neutral” pulse width where the motor stops. This is typically around 1500 µs, but slight variations exist. Run the calibration script to find the exact value for your servo:
bash

python calibration.py

The script will sweep pulse widths and ask you to observe the servo. Enter the value where it stops completely. Update the STOP_PULSE constant in tracking.py with that value.
Running the Tracker

With the servo connected and the camera ready, run:
bash

python tracking.py

A window will open showing the camera feed. The servo will rotate to follow your face. Press q to exit.
Configuration

Edit the parameters in tracking.py to adjust behaviour:
Parameter	Description	Typical value
STOP_PULSE	Pulse width (µs) at which the servo stops	1500
PULSE_MIN	Minimum allowed pulse (full reverse)	1000
PULSE_MAX	Maximum allowed pulse (full forward)	2000
DEAD_ZONE	Pixels from center where no movement occurs (640px width)	80–120
Kp	Proportional gain (speed per pixel error)	0.0005–0.002
MAX_SPEED	Maximum speed factor (0–1)	0.05–0.2
MIN_SPEED	Minimum speed threshold (ignored below this)	0.02
UPDATE_INTERVAL	Seconds between servo commands	0.1–0.2
NO_DETECTION_TIMEOUT	Seconds before stopping after lost face	2.0
FRAME_WIDTH, FRAME_HEIGHT	Camera resolution	640, 480
DETECTION_WIDTH, DETECTION_HEIGHT	Downscaled size for detection	320, 240
Troubleshooting
Servo does not move or moves erratically

Check power supply: the servo needs a separate 5V source with enough current.

Verify the signal wire is connected to GPIO 18.

Run pigpiod (sudo pigpiod) and ensure it’s running.

Calibrate STOP_PULSE again; even a few µs off can cause drift.

Face not detected
Ensure the camera works: test with libcamera-hello (CSI) or ffplay /dev/video0 (USB).

Adjust lighting; Haar cascades need decent illumination.

Modify minNeighbors or scaleFactor in detectMultiScale for better sensitivity.

Servo oscillates or overshoots

Reduce Kp (gain).

Increase DEAD_ZONE.

Lower MAX_SPEED.

Add smoothing (already included as low‑pass filter on face position).

License

This project is licensed under the MIT License – see the LICENSE file for details.
Acknowledgements

OpenCV for the face detection cascade.

pigpio for precise servo control.

Inspired by countless Raspberry Pi tracking projects.

Happy tracking!
