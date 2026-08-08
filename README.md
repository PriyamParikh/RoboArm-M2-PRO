# RoArm-M2-S Production Joint & Trajectory Controller

A production-oriented Python GUI for controlling the **RoArm-M2-S 4DOF Desktop Robotic Arm** through direct USB serial communication with the onboard ESP32.

The software provides real-time joint control, forward kinematics visualization, waypoint-based trajectory planning, ESP32 mission-file management, and an integrated serial monitor without requiring the computer to disconnect from its normal Wi-Fi network.

---

## Features

* USB serial communication with the RoArm-M2-S onboard ESP32
* Automatic COM-port detection
* Individual servo control using sliders
* Base, Shoulder, Elbow and Gripper control
* Live slider-based joint movement
* Synchronized multi-joint movement
* Adjustable servo speed and acceleration
* Forward kinematics visualization
* Real-time firmware position feedback
* Joint-angle feedback
* Cartesian X, Y and Z position feedback
* Waypoint creation and editing
* Joint-space trajectory planning
* Linear trajectory interpolation
* Cubic trajectory interpolation
* Quintic trajectory interpolation
* Smooth trajectory execution
* Trajectory preview graphs
* Adjustable trajectory duration
* Adjustable trajectory command frequency
* Pause, Resume and Stop trajectory functions
* CSV trajectory export
* ESP32 mission-file creation
* Mission-file listing
* Mission playback
* Repeat mission execution
* Mission reset
* Mission deletion
* Save waypoint sequences as missions
* Replace existing missions with new waypoint sequences
* Integrated serial monitor
* Serial monitor clearing
* Serial logging
* Serial connection reset
* Firmware debug ON/OFF control
* Emergency Stop and Reset E-Stop controls

---

# Hardware

This software is designed for:

**RoArm-M2-S 4DOF Desktop Robotic Arm**

The robot uses an onboard ESP32 controller and serial-bus servo system.

Typical connection:

```text
12 V Power Supply
       │
       ▼
   RoArm-M2-S

Computer USB
       │
       ▼
RoArm USB Type-C Port

Computer Wi-Fi
       │
       ▼
Normal Internet / Router
```

The robotic arm communicates with the computer using USB serial communication, so the computer can remain connected to its regular Wi-Fi network.

---

# Software Requirements

Recommended:

```text
Python 3.10+
Windows 10 / Windows 11
```

Required Python libraries:

```text
pyserial
matplotlib
```

Tkinter is normally included with standard Python installations on Windows.

---

# Installation

Clone or download the repository.

Open Command Prompt or PowerShell inside the project directory.

Install the required libraries:

```bash
python -m pip install pyserial matplotlib
```

or:

```bash
python -m pip install -r requirements.txt
```

---

# Running the Application

Run:

```bash
python roarm_m2_production_joint_trajectory_gui_v4_1.py
```

The graphical interface should open automatically.

---

# Connecting the RoArm-M2-S

1. Connect the RoArm-M2-S to its recommended power supply.
2. Connect the robot's USB control port to the computer.
3. Start the Python GUI.
4. Click **Refresh**.
5. Select the detected COM port.
6. Click **Connect**.

The application communicates at:

```text
115200 baud
```

The RoArm-M2-S generally appears as a CP210x USB-to-UART serial device.

---

# Joint Control

The Joint Control tab provides individual control of:

```text
Joint 1 - Base
Joint 2 - Shoulder
Joint 3 - Elbow
Joint 4 - Gripper / End Effector
```

The approximate configured ranges are:

| Joint    |          Range |
| -------- | -------------: |
| Base     | -180° to +180° |
| Shoulder |   -90° to +90° |
| Elbow    |     0° to 180° |
| Gripper  |    45° to 180° |

Always confirm the mechanical limits of your specific robot before operating close to extreme angles.

---

# Live Slider Control

When **Live Slider Control** is enabled, moving a slider immediately commands the corresponding servo.

Single-joint movement uses the RoArm JSON command:

```json
{
  "T": 121,
  "joint": 1,
  "angle": 20,
  "spd": 10,
  "acc": 5
}
```

Example joint numbering:

```text
1 = Base
2 = Shoulder
3 = Elbow
4 = Gripper
```

A debounce mechanism is used to reduce excessive serial traffic when the slider is moved rapidly.

---

# Synchronized Joint Movement

The **MOVE ALL JOINTS** button sends all four joint targets simultaneously.

Example:

```json
{
  "T": 122,
  "b": 0,
  "s": 20,
  "e": 100,
  "h": 150,
  "spd": 15,
  "acc": 8
}
```

This mode is useful when moving the complete arm toward a predefined configuration.

---

# Forward Kinematics

The software provides two forms of forward-kinematics information.

## Firmware FK

The application requests robot feedback using:

```json
{
  "T": 105
}
```

The RoArm firmware can return information including:

```text
X
Y
Z
Base angle
Shoulder angle
Elbow angle
Gripper angle
```

The firmware-generated Cartesian coordinates should be treated as the primary positional reference because they reflect the robot's internal kinematic configuration and calibration.

---

## Local FK Visualization

The GUI also contains a graphical forward-kinematics preview.

The arm configuration updates automatically when the joint sliders are changed.

This visualization is intended for:

* Educational understanding
* Approximate workspace visualization
* Joint-position inspection
* Trajectory planning support

It should not be considered a collision-detection system.

---

# Trajectory Planning

The GUI provides waypoint-based joint-space trajectory planning.

Available trajectory methods:

```text
Linear
Cubic
Quintic
```

---

## Linear Trajectory

Linear interpolation moves uniformly between two joint configurations.

It is simple but may introduce abrupt velocity changes at waypoint boundaries.

---

## Cubic Trajectory

Cubic interpolation provides smoother velocity transitions than basic linear interpolation.

It is suitable for general robotic motion demonstrations.

---

## Quintic Trajectory

Quintic interpolation is recommended for smooth robotic movement.

It provides zero velocity and zero acceleration at the beginning and end of trajectory segments.

For most demonstrations, use:

```text
Trajectory Method: Quintic
```

---

# Creating a Trajectory

Example workflow:

1. Configure the four joint sliders.
2. Click **Add Slider Pose**.
3. Change the slider positions.
4. Add another waypoint.
5. Continue adding required poses.
6. Select Linear, Cubic or Quintic interpolation.
7. Define the time required to reach each waypoint.
8. Click **Generate / Preview**.
9. Inspect the trajectory graph.
10. Click **EXECUTE TRAJECTORY**.

---

# Example Pick-and-Place Waypoints

A typical mission may contain:

```text
P1 - Home
P2 - Move forward
P3 - Above target
P4 - Lower toward object
P5 - Open gripper
P6 - Close gripper
P7 - Lift object
P8 - Return path
P9 - Home
```

The gripper can be treated as the fourth joint and can therefore be included directly in the trajectory.

---

# Recommended Initial Trajectory Settings

For initial testing:

```text
Method: Quintic

Command Rate:
10-20 Hz

Segment Duration:
2-4 seconds

Joint Speed:
10-20 deg/s

Acceleration:
5-10
```

After reliable operation has been confirmed, higher speeds can be tested gradually.

---

# Trajectory Controls

The trajectory interface provides:

```text
Generate / Preview
Execute Trajectory
Pause
Resume
Stop
Export CSV
```

The generated trajectory graph displays joint angle against time for:

```text
Base
Shoulder
Elbow
Gripper
```

---

# CSV Export

Generated trajectory information can be exported for research or analysis.

The CSV contains values such as:

```text
time_s
base_deg
shoulder_deg
elbow_deg
gripper_deg
```

This can be useful for:

* Research-paper graphs
* Motion analysis
* Trajectory comparison
* Experimental documentation
* Machine-learning datasets

---

# Mission Files

The RoArm-M2-S can store mission files in the onboard ESP32 Flash memory.

The GUI provides a dedicated Mission Files interface.

Available functions include:

```text
Create Mission
Refresh Mission List
Read Mission
Play Mission
Repeat Mission
Reset Mission
Delete Mission
Save Waypoints to Mission
Replace Mission with Waypoints
```

---

# Create Mission

Enter a mission name such as:

```text
pick_and_place
```

Then click:

```text
Create
```

The mission is created in ESP32 Flash memory.

---

# Save Waypoints to Mission

A waypoint sequence can be transferred to the robot as mission commands.

This makes it possible to design the sequence graphically inside Python and subsequently execute it directly from the robot mission system.

---

# Replace Mission with Waypoints

This option:

1. Removes the existing mission.
2. Recreates the mission.
3. Saves the current waypoint sequence.

This is useful when modifying an existing experiment.

---

# Reset Mission

The **Reset / Empty Mission** function removes all previously stored steps from a selected mission and recreates an empty mission using the same name.

This is useful during iterative robot programming.

---

# Mission Playback

A mission can be executed once or repeated.

Example:

```text
Repeat = 1
```

executes once.

Higher values repeat the mission multiple times.

The firmware may also support:

```text
-1
```

for continuous mission playback.

Use continuous playback carefully and keep access to the emergency stop.

---

# Serial Monitor

The application contains an integrated serial monitor.

It displays transmitted and received messages such as:

```text
TX {"T":121,"joint":1,"angle":20,"spd":10,"acc":5}

RX {"T":1051,...}
```

Available monitor tools:

```text
Clear Display
Pause Display
Resume Display
Save Full Log
Reset Serial Connection
Firmware Debug ON
Firmware Debug OFF
```

---

# Serial Connection Reset

If communication becomes unresponsive, use:

```text
Reset Serial Connection
```

The software closes and reopens the currently selected COM port.

This is useful after:

* Temporary USB disconnection
* ESP32 restart
* Serial communication interruption
* Firmware debug issues

---

# Emergency Stop

The GUI provides an Emergency Stop command.

Emergency stop:

```json
{
  "T": 0
}
```

Reset E-Stop:

```json
{
  "T": 999
}
```

Software emergency stopping still depends on successful serial communication.

For laboratory development, always keep access to the physical power switch.

---

# Recommended First Test

Before creating a complete trajectory:

1. Remove any payload.
2. Secure the robot properly.
3. Clear the workspace.
4. Connect the USB cable.
5. Connect the robot power supply.
6. Start the GUI.
7. Select the COM port.
8. Click **Connect**.
9. Set speed to approximately `10 deg/s`.
10. Set acceleration to approximately `5`.
11. Move only the Base slider by approximately 5°.
12. Confirm the correct direction.
13. Test Shoulder.
14. Test Elbow.
15. Test Gripper.
16. Request current pose.
17. Test a simple two-waypoint trajectory.
18. Only then proceed to complete robotic tasks.

---

# Suggested Research Workflow

The software can be used for robotic-arm research involving:

* Forward kinematics
* Joint-space motion planning
* Trajectory interpolation
* Pick-and-place tasks
* Assistive robotic manipulation
* Human-robot interaction
* Motion repeatability experiments
* Robot-control education
* Path-planning comparison
* Trajectory dataset generation
* Robotic feeding applications
* Mechatronics teaching

---

# Software Architecture

```text
                    Python GUI
                        │
        ┌───────────────┼─────────────────┐
        │               │                 │
        ▼               ▼                 ▼
   Joint Control   Trajectory Planner   Mission Manager
        │               │                 │
        └───────────────┼─────────────────┘
                        │
                        ▼
                  JSON Commands
                        │
                        ▼
                  USB Serial
                        │
                        ▼
                Onboard ESP32
                        │
                        ▼
              RoArm-M2-S Servos
                        │
                        ▼
               Position Feedback
                        │
                        ▼
                  Python GUI
```

---

# Dependencies

```text
Python
Tkinter
PySerial
Matplotlib
CSV
Threading
JSON
Math
```

Only the external packages below normally require installation:

```bash
python -m pip install pyserial matplotlib
```

---

# Limitations

The current software does not perform:

* Automatic collision detection
* Obstacle avoidance
* Dynamic payload estimation
* Force control
* Vision-based manipulation
* ROS integration
* Automatic inverse kinematics from arbitrary Cartesian targets

These features can be integrated in future versions.

---

# Safety

Robotic arms can cause mechanical damage or personal injury if operated incorrectly.

Always:

* Secure the robot to a rigid surface.
* Test without payload first.
* Use low speed during initial testing.
* Avoid mechanical joint limits.
* Keep hands outside the active workspace.
* Maintain access to the physical power switch.
* Verify waypoint sequences before automatic execution.
* Never rely solely on software emergency stopping.

---

# Repository Structure

```text
RoArm-M2-Controller/
│
├── roarm_m2_production_joint_trajectory_gui_v4_1.py
├── requirements.txt
└── README.md
```

---

# Future Development

Potential future improvements include:

* Cartesian inverse kinematics control
* 3D workspace visualization
* Collision detection
* Obstacle-aware trajectory planning
* Velocity and acceleration graphs
* Real-time joint telemetry
* Automatic homing sequence
* Mission-file editor
* Camera-based object detection
* Pick-and-place automation
* ROS/ROS2 integration
* Digital-twin visualization
* Machine-learning-based motion planning
* Research-data logging
* Raspberry Pi deployment

---

# License

Choose an appropriate license before public distribution.

For open-source academic and research use, commonly used options include:

```text
MIT License
Apache License 2.0
GNU GPL v3
```

---

# Disclaimer

This software is an independent research and educational control interface developed for the RoArm-M2-S robotic arm.

RoArm-M2-S and related firmware/hardware names belong to their respective manufacturers.

The software should be tested carefully before use in safety-critical, medical, industrial or human-interaction applications.
