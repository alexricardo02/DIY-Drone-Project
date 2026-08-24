# DIY Drone Project: From Racing Frame to F450 and Back — Pixhawk to JHEMCU Stack Migration
 
[![Build Status](https://img.shields.io/badge/Build-In--Progress-orange.svg)]()
[![Hardware](https://img.shields.io/badge/FC-JHEMCU_F405_NOXE_V2-blue.svg)]()
[![Firmware](https://img.shields.io/badge/Firmware-Betaflight-green.svg)]()
 
This repository documents the full evolution, build process, and configuration history of a custom-built analog FPV quadcopter. The project has gone through several hardware iterations — including a flight controller and ESC migration — while converging toward a lightweight, racing-oriented setup.
 
### Current Status
 
The build has migrated from a Pixhawk 2.4.8 / ArduPilot setup to a JHEMCU F405 NOXE V2 / Betaflight setup. All soldering for the new stack is complete (flight controller, ESC, receiver, VTX, camera, and GPS power). The project is currently in the Betaflight initial configuration phase, working through USB connectivity and RX/VTX/GPS setup on Linux Mint.
 
---
 
## Table of Contents
 
1. [Project Vision](#project-vision)
2. [Bill of Materials](#bill-of-materials)
3. [Project Timeline and Evolution](#project-timeline-and-evolution)
   - [Phase 1: Racing Foundation (Pixhawk Era)](#phase-1-racing-foundation-pixhawk-era)
   - [Phase 2: The F450 Frame Experiment](#phase-2-the-f450-frame-experiment)
   - [Phase 3: The Pixhawk Troubleshooting Chronicles](#phase-3-the-pixhawk-troubleshooting-chronicles)
   - [Phase 4: Decision to Migrate — JHEMCU F405 NOXE V2](#phase-4-decision-to-migrate--jhemcu-f405-noxe-v2)
   - [Phase 5: New Stack Assembly and Soldering](#phase-5-new-stack-assembly-and-soldering)
   - [Phase 6: Betaflight Configuration](#phase-6-betaflight-configuration)
4. [Software and Parameter Configuration History](#software-and-parameter-configuration-history)
5. [Media Gallery](#media-gallery)
6. [Future Roadmap](#future-roadmap)
---
 
## Project Vision
 
The core objective of this project is to build a fully functional analog FPV racing quadcopter from individual components, developing a working understanding of flight controller logic, ESC signaling, radio link architecture, and video transmission chains along the way. The project originally targeted ArduPilot on a Pixhawk-based platform, but was later redirected toward a Betaflight-based racing stack after encountering fundamental hardware incompatibilities described below.
 
---
 
## Bill of Materials
 
### Legacy Hardware (Pixhawk Era — retired)
 
- **Flight Controller:** Pixhawk 2.4.8, mounted on a vibration-damping plate
- **Receiver:** FlySky FS-iA6B 2.4GHz 6CH (PPM/i-BUS capable)
- **Power Distribution Board:** Matek PDB-XT60, with integrated current sensor and dual BEC (5V/12V)
- **Diagnostics tool used:** MultiWiiConf for sensor visualization and orientation checks
- **Ground control software:** Mission Planner
### Current Hardware (JHEMCU Stack — active build)
 
- **Flight Controller:** JHEMCU F405 NOXE V2 (integrated barometer)
- **ESC:** JHEMCU 4-in-1 40A ESC, also functioning as the power distribution board
- **Motors:** Racerstar BR2205 2300KV brushless motors
- **Frame:** 210-250mm carbon fiber racing frame (3mm/4mm arms)
- **Propellers:** 5045 3-blade high-efficiency propellers
- **Receiver:** FlySky FS-iA6 2.4GHz 6CH (PWM-only, not i-BUS/PPM native) + external PPM encoder module
- **VTX:** SpeedyBee TX800 analog video transmitter (3.7V-5.5V input range, 5V rail only)
- **Camera:** Caddx-type analog FPV camera
- **GPS/Compass:** Combo GPS module with integrated compass
- **Peripherals:** External buzzer
- **Battery:** 1550mAh 3S/4S LiPo
- **Software:** Betaflight Configurator (web app, app.betaflight.com)
- **Unused onboard connector:** FC 6-pin HD connector, reserved for digital HD systems (DJI O3 / Walksnail), not used in this analog build
### Hardware Retired During This Project
 
- Pixhawk 2.4.8 flight controller
- Matek PDB-XT60 power distribution board
- FlySky FS-iA6B receiver (replaced with FS-iA6 + PPM encoder)
### Experimental / Unused Parts
 
- Raspberry Pi 4 (kept in the parts box for potential future companion-computer integration)
---
 
## Project Timeline and Evolution
 
### Phase 1: Racing Foundation (Pixhawk Era)
 
Initial assembly on the carbon fiber racing frame. The build centered around a Pixhawk 2.4.8 mounted on a vibration-damped plate to isolate the IMU from frame vibration, paired with a Matek PDB-XT60 for power distribution and a JHEMCU EM40A 4-in-1 ESC for motor control (BLHeli_S, DShot600 capable).
 
### Phase 2: The F450 Frame Experiment
 
An attempt was made to migrate the electronics onto a larger, utility-style F450 frame with plastic/nylon arms.
 
- **The failure:** the 2300KV motors were mismatched with the larger propellers required by the F450 frame, resulting in excessive motor heating, severe structural vibration, and poor overall efficiency.
- **The outcome:** the change was reverted, and the build returned to the original carbon fiber racing frame, which better matched the high-KV motors.
### Phase 3: The Pixhawk Troubleshooting Chronicles
 
A detailed log of the hardware and software issues encountered while bench-testing the Pixhawk-based configuration.
 
**1. The ESC signal voltage gap (3.3V vs 5V)**
 
- *Problem:* the JHEMCU EM40A ESC (BLHeli_S) requires a 5V logic signal, while the Pixhawk 2.4.8 outputs a 3.3V signal.
- *Symptom:* motors beeped on arm but refused to spin.
- *Diagnosis:* multimeter testing measured signal pulses in the 0.21V-0.35V to ~1.52V range depending on test conditions — well below the threshold needed to reliably trigger the ESC's modern processor.
- *Mitigation attempted:* a bypass test connecting the ESC signal wire directly to receiver Channel 3 confirmed the ESC hardware itself was functional.
- *Resolution:* this logic-level mismatch was ultimately a primary driver behind retiring the Pixhawk in favor of a 5V-native flight controller (see Phase 4).
**2. Radio failsafe and calibration issues**
 
- *Problem:* Mission Planner reported a constant "Radio Failsafe" with throttle stuck at 900-901.
- *Diagnosis:* incorrect pinout on the FS-iA6B receiver — the signal wire was physically wired to the receiver instead of being routed through the Pixhawk RC input during calibration.
- *Resolution:* standardized the PPM/i-BUS connection and adjusted `FS_THR_VALUE` below 950 to prevent pre-arm triggers.
**3. The "Compass 3 Not Found" bug**
 
- *Problem:* the ArduPilot firmware searched for a non-existent third internal compass, blocking arming.
- *Resolution:* disabled `COMPASS_USE3` in the Full Parameter List to clear the pre-arm safety check.
### Phase 4: Decision to Migrate — JHEMCU F405 NOXE V2
 
Given the recurring logic-level conflicts between the Pixhawk (3.3V signaling) and the racing-grade JHEMCU ESC (5V native), the decision was made to retire the Pixhawk/ArduPilot combination entirely rather than continue working around it with level shifters. The project moved to a JHEMCU F405 NOXE V2 flight controller to unify the electronics around a single, racing-native 5V ecosystem and to eliminate the external Matek PDB, since power and motor signal distribution are handled directly through an 8-pin ribbon cable between the FC and ESC. This also meant switching the flight software stack from ArduPilot/Mission Planner to Betaflight.
 
### Phase 5: New Stack Assembly and Soldering
 
With the new flight controller in hand, the build moved into the soldering and physical wiring phase.
 
**FC-ESC link:** the 8-pin ribbon cable connecting the JHEMCU F405 NOXE V2 to the 4-in-1 ESC carries power (VBAT, GND) and the four motor signal lines (S1-S4) — not the motor phase wires themselves. Motors remain soldered directly to the ESC.
 
**Receiver wiring:** since the FS-iA6 is a PWM-only receiver (unlike the FS-iA6B used in the Pixhawk era, which supported PPM/i-BUS natively), a dedicated PPM encoder module was introduced. Wiring chain: FS-iA6 channels CH1-CH6 feed into the PPM encoder's inputs, and the encoder's single PPM output connects to the RX1 pad on the flight controller. Betaflight's receiver mode must be set to PPM to match this wiring.
 
**Video system wiring:** the analog video chain was wired so the flight controller could overlay OSD data onto the video feed before it reaches the VTX. The camera's power leads connect to the ESC/PDB, while only the camera's yellow video signal wire goes to the FC's CAM pad; the FC's VTX pad then feeds the combined (video + OSD) signal to the SpeedyBee TX800.
 
**VTX wiring (SpeedyBee TX800):** the TX800 accepts 3.7V-5.5V input only, so it is powered from the 5V rail exclusively — not any higher-voltage pad. Cable assignments: red = VCC (5V), black = GND, yellow = video signal (to the FC's VTX pad), green = IRC Tramp control line (to a UART TX pad, enabling in-flight VTX power/channel control from Betaflight).
 
**GPS/Compass wiring:** power wires (red/black) were soldered first, as these are unambiguous across GPS modules. The four data wires (gray, green, white, yellow) were left unsoldered pending verification against the specific GPS module's PCB silkscreen labeling, since pin assignments are not standardized across modules and incorrect wiring here risks damaging the module or the FC's UART/I2C peripherals.
 
**Buzzer:** wired per standard polarity to the FC's dedicated buzzer pads.
 
**Unused connector:** the FC's 6-pin HD connector was left unpopulated, as it is intended for digital HD video systems (DJI O3 / Walksnail) and has no role in this analog build.
 
### Phase 6: Betaflight Configuration
 
With soldering complete, the project moved to flashing and configuring Betaflight via the web-based Betaflight Configurator (app.betaflight.com) on Linux Mint with Chromium.
 
**USB connectivity issue:** the initial connection attempt failed with a "Failed to open serial port" error.
 
- *Diagnosis:* the flight controller was correctly detected at `/dev/ttyACM0`, and the port itself was not in use by another process. The root cause was identified as a Linux permissions issue — the user account had been added to the `dialout` group (required for serial port access), but this group membership had not yet taken effect in the active login session.
- *Resolution identified:* a full system reboot with the flight controller disconnected, so the new group membership is applied cleanly when the session restarts. Simply closing and reopening a terminal is not sufficient, since `usermod` group changes require a new login session.
Remaining configuration steps (RX input mode, VTX protocol, GPS setup) are pending successful reconnection after the reboot; see Future Roadmap.
 
---
 
## Software and Parameter Configuration History
 
### Legacy: ArduPilot Parameters (Pixhawk Era)
 
| Parameter | Value | Description |
|---|---|---|
| `MOT_PWM_TYPE` | 0 (Normal) | Forced to PWM due to Pixhawk 2.4.8 MAIN port limitations |
| `BRD_SAFETYENABLE` | 0 | Disabled for bench testing to bypass the hardware safety button |
| `FRAME_CLASS` | 1 | Configured as quadcopter |
| `FRAME_TYPE` | 1 | Configured as X-frame |
| `MOT_SPIN_ARM` | 0.15 | Increased idle spin to overcome high-KV motor inertia |
| `COMPASS_USE3` | Disabled | Cleared a false "Compass 3 Not Found" pre-arm check |
| `FS_THR_VALUE` | <950 | Adjusted to prevent false pre-arm failsafe triggers |
 
### Current: Betaflight Configuration (JHEMCU Era)
 
| Setting | Target Value | Status |
|---|---|---|
| Receiver mode | PPM | Pending — set after USB reconnection |
| VTX protocol | IRC Tramp, on the UART connected to the TX800's control line | Pending |
| GPS | Enabled, port TBD once data wires are soldered | Pending |
| USB serial connection | `/dev/ttyACM0`, blocked by `dialout` group permissions | Fix identified, reboot pending |
 
---
 
## Media Gallery
 
### Build Process (Pixhawk Era)
 
| Pixhawk 2.4.8 | Racerstar 2300KV Motor | Internal Wiring |
|:---:|:---:|:---:|
| ![Pixhawk](pixhawk_connections.jpg.jpg) | ![Motor](motor_racerstar_2205.jpg.jpg) | ![Wiring](wiring.jpeg) |
 
### Testing and Results
 
- **Stability tests (Pixhawk era):** working toward stable hover before moving to manual/acro modes.
- **Log analysis (Pixhawk era):** reviewing flight data to minimize vibration and improve response time.
---
 
## Future Roadmap
 
- [ ] Reboot the system with the flight controller disconnected to apply `dialout` group permissions
- [ ] Reconnect to Betaflight Configurator and confirm firmware/target detection
- [ ] Set receiver mode to PPM and verify channel mapping from the FS-iA6/PPM encoder chain
- [ ] Configure VTX protocol as IRC Tramp on the correct UART and verify power/channel control from Betaflight
- [ ] Verify the four GPS data wires (gray, green, white, yellow) against the module's PCB silkscreen, then solder and configure the GPS port/protocol in Betaflight
- [ ] Configure OSD elements now that the camera-FC-VTX video chain is wired
- [ ] Set up motor protocol (DShot300/DShot600) and confirm motor direction/order
- [ ] Perform first-arm bench test with propellers removed
- [ ] PID tuning once stable hover is achieved
- [ ] Evaluate Raspberry Pi 4 companion-computer integration as a longer-term stretch goal
---
