# Scope
A repo for the Ground Station and onboard UAV applications.

## Planned Implementation

A companion-computer payload system for a fixed-wing UAV testbed, using the **SpeedyBee F405 Wing** as the ArduPilot flight controller and a **Raspberry Pi + AI HAT+** as the onboard mission/payload computer.

### System architecture

* **Flight controller to Pi:** the FC sends live **ArduPilot MAVLink telemetry** to the Raspberry Pi over a **UART link**.
* **Pi to ground station:** the Pi forwards telemetry and payload data to the ground over the telemetry link, while also supporting a custom dashboard.
* **Power:** both FC and Pi are powered from the **main aircraft battery** through separate branches; the Pi is powered by its **own 5V regulator**, not from the FC rail.
* **Video roles:** **Walksnail** remains the live FPV/operator video system, while the **Raspberry Pi camera** is used for onboard perception and slow image updates.

### Onboard software plan

* **C++ main application** for:

  * MAVLink telemetry ingestion from the FC
  * message routing and forwarding
  * image scheduling and rate limiting
  * payload state machine
  * runtime settings/control handling
  * event generation and logging
* **Python sidecar/tooling** for:

  * object detection model loading/inference
  * model experimentation and offline analysis

### Ground system plan

A custom dashboard that can display:

* live aircraft telemetry in a **HUD-style view**
* mission/vehicle data normally shown through ArduPilot telemetry
* a slow-updating **Pi camera image pane**
* a **last detected object** image/crop panel
* payload events such as detections, confidence, IDs, and coordinates
* runtime settings that can be changed remotely and sent back to the Pi

### Telemetry and image strategy

* Normal aircraft telemetry remains live continuously.
* Payload detections are sent as **compact live messages** such as coordinates, labels, confidence, and status.
* Pi camera imagery is treated as a **slow snapshot channel**, not true video.
* Planned image behavior:

  * low-rate baseline image refresh
  * immediate forced image update on detection
  * return to slow refresh afterward
* Full-resolution imagery is stored onboard; only compressed snapshots/crops are sent to the ground.

### Proposed features

* live ArduPilot telemetry mirrored into a custom dashboard
* onboard detection event reporting
* slow image retrieval from the Pi camera
* last-detection thumbnail/crop display
* adjustable onboard payload settings from the ground station
* onboard logging of telemetry, detections, and captured imagery

### Airframe role

The system is intended to be developed first on the **Stallion** as a simpler testbed, then potentially migrated to a larger payload-oriented platform like the **Stork**. The Stallion is suitable for payload iteration because its nose is removable and customizable. 

