# DIY Drone Project: From Racing Frame to F450 and Back

This repository documents the evolution, build process, and flight testing of my custom-built quadcopter. The project has undergone several iterations, experimenting with different frames and power configurations to find the optimal balance of agility and stability.

## 🚀 Project Overview
The primary goal is to build a versatile drone using the **Pixhawk 2.4.8** flight controller. 

### Current Status:
After experimenting with a larger **F450 frame** (plastic/nylon arms), I decided to revert to the **Carbon Fiber Racing Frame**. The high-KV motors and lightweight structure proved to be a much better match for the agility I'm looking for.

---

## 🛠️ Hardware Specifications

* **Flight Controller:** Pixhawk 2.4.8 (PX4/ArduPilot compatible).
* **Motors:** Racerstar BR2205 2300KV Brushless.
* **PDB (Power Distribution Board):** Matek PDB-XT60 with integrated current sensor.
* **Frame 1 (Current):** 5-inch Carbon Fiber Racing Frame (3mm/4mm arms).
* **Frame 2 (Experimental):** F450 Flame Wheel style (Red/White arms) - *Currently decommissioned*.
* **Configuration Software:** Mission Planner / QGroundControl / MultiWiiConf.

---

## 📈 Design Evolution

### Phase 1: Initial Build (Racing Frame)
The project started with a focus on speed. I integrated the Pixhawk onto a lightweight carbon fiber chassis. Early sensor testing was conducted using IMU visualization tools to ensure proper accelerometer and gyro calibration.

### Phase 2: The F450 Experiment
I attempted to migrate the electronics to a larger F450 frame to gain more mounting space and stability. However, the 2300KV motors were too high-revving for the large propellers typically used on this frame, leading to poor efficiency and vibrations.

### Phase 3: Refinement & Flight Testing
Returning to the racing frame allowed for much tighter PID tuning. This stage involved:
* Static thrust tests and ESC calibration.
* Real-time telemetry monitoring via MultiWiiConf.
* Field testing, including high-speed maneuvers and safety failsafe checks.

---

## 📸 Media Gallery

### Build Process
| Pixhawk 2.4.8 Setup | Racerstar 2300KV Motor | Internal Wiring |
| :---: | :---: | :---: |
| ![Pixhawk](./media/photos/pixhawk_board.jpg) | ![Motor](./media/photos/motor_close_up.jpg) | ![Wiring](./media/photos/wiring_setup.jpg) |

### Testing & Results
* **Stability Tests:** Achieving stable hover before moving to manual/acro modes.
* **Log Analysis:** Reviewing flight data to minimize vibrations and improve response time.

---

## ⚙️ Software & Tools
* **Firmware:** ArduCopter / PX4.
* **Diagnostics:** `MultiWiiConf` for real-time sensor visualization and orientation checks.
