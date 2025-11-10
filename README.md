# 🔭 Overview – AI Gesture Control for 3D Workflows

## Description

MotionCoder turns **sensor streams** into **semantic gestures/intents** in real time, then routes them to your **target software** via lightweight connectors.
The stack is **modular** (Sensors → AI Interpretation → Connectors), **low-latency**, and optimized for **CAD/DCC**—yet portable to many other domains.

For a quick preview, here’s [YouTube example](https://www.youtube.com/watch?v=923FFy5cI-4). It demonstrates only **~2–3%** of the intended capability and runs on non-precision demo hardware. MotionCoder’s goal is **full, high-accuracy gesture coverage**, informed by my hands-on experience in gesture design and control.


## 🎥 Layer 1 – Sensor I/O

*Note: Choose the module based on your budget! More in the GitHub docs: 👉 [Sensor Guide](https://github.com/xtanai/sensor-guide)*

| 🧩 **Module**  | 📝 **Short Description**                                                                                                                                                       | 🔌 **Hardware / Deps**                                                       | ⚖️ **License** | ⚠️ **Notes**                                                            | 🚦 **Status**         | 🔗 **Link** |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------- | -------------- | ----------------------------------------------------------------------- | --------------------- | ----------- |
| **Pi5Track3D** | **RAW10 mono ingest** on Pi 5; GPU/NEON-optimized preproc (undistort/normalize), optional on-Pi keypoints; streaming to host for triangulation.                                | **Raspberry Pi 5** (4/8 GB), 2× MIPI-CSI (OV9281 etc.), NVMe                 | Apache-2.0     | —                                                                       | 🟡 Planned            | coming soon |
| **MVCore3D**   | **MJPEG ingest** (libjpeg-turbo), triangulation via **Anipose**, 2D keypoints via **MMPose**; **CPU-tuned** plug-and-play for multi-cam.                                       | USB-UVC cams (incl. low-cost), optional sync                                 | Apache-2.0     | —                                                                       | 🟠 Later              | coming soon |
| **MVYUV3D**    | **Uncompressed YUV ingest** (**YUY2/UYVY 4:2:2**, optional **NV12 4:2:0**); lower CPU load than MJPEG, higher USB load than MONO8; triangulation **Anipose**, pose **MMPose**. | UVC cams with YUV output; stable lighting; optional soft/hard sync           | Apache-2.0     | —                                                                       | 🟠 Later              | coming soon |
| **MVRaw3D**    | **RAW10/12 (Bayer/Mono) ingest**, debayer/denoise pipeline; **lower latency & higher fidelity** vs. MJPEG; GPU-accelerated where available.                                    | Global-/rolling-shutter cams; **HW sync recommended**                        | Apache-2.0     | —                                                                       | 🟠 Later              | coming soon |
| **MVMono3D**   | **Synchronized mono multi-cam** (global shutter) with high occlusion robustness & precision; NIR-ready.                                                                        | 3–4 mono cams with Interface **SFP+**, GPUDirect                             | Apache-2.0     | —                                                                       | 🟠 Targeted next year | coming soon |
| **TDMStrobe**  | **Time-Division-Multiplexed IR strobe & camera trigger** (phase control A/B/C/D) for MV* pipelines and Pi5Track3D                                                              | IR-LED/VCSEL arrays, MOSFET drivers, MCU (EPS32)                             | Apache-2.0     | —                                                                       | 🟡 Planned            | coming soon |
| **Leap2Pose**  | **LeapC ingestion → normalized poses/streams** (consistent joints & units) for downstream adapters.                                                                            | Leap Motion Controller 1/2                                                   | MIT            | —                                                                       | 🟢 Active             | coming soon |


## 🧠 Layer 2 – AI Interpretation (Pose → Intents/Gestures)

| 🧩 **Module**        | 📝 **Short Description**                                 | 🔁 **I/O**                  | ⚖️ **License** | ⚠️ **Notes** | 🚦 **Status**  | 🔗 **Link**                                                                   |
| -------------------- | --------------------------------------------------------- | ---------------------------- | -------------- | ------------ | -------------- | ------------------------------------------------------------------------------ |
| **MotionCoder**      | Real-time gestures/intents, state machine, context logic. | Poses/keypoints from Layer 1 | Apache-2.0     | —            | 🟡 In progress | [MotionCoder](https://github.com/xtanai/motioncoder) |
| **Pose2Gizmo**       | Poses → 3D manipulator/gizmo commands & visualization.    | Normalized poses, tool hints | MIT            | —            | 🟡 Planned     | coming soon   |

## 🔗 Layer 3 – Connectors (DCC/CAD/Engines)

| 🧩 **Module**           | 📝 **Short Description**                               | 🖥️ **Target System** | ⚖️ **License** | ⚠️ **Notes** | 🚦 **Status**                 | 🔗 **Link**                                                                         |
| ------------------------ | ------------------------------------------------------ | --------------------- | -------------- | ------------ | ----------------------------- | ------------------------------------------------------------------------------------ |
| **Coder2Blender**        | Add-on/API bridge: gestures → operators/hotkeys/nodes. | Blender               | MIT            | —            | 🟡 Research (API exploration) | coming soon  |
| **Coder2Unreal**         | Plugin/bridge: gestures → Blueprint/C++ events.        | Unreal Engine         | MIT            | —            | 🟡 Planned                    | coming soon   |
| **Coder2Dassault**       | Macro/API bridge: gestures → CAD commands.             | Dassault (SWX/CATIA)  | MIT            | —            | 🟠 Targeted for next year     | coming soon |


**Why separate (MotionCoder ↔ Coder2$)?**

* **Modularity:** MotionCoder (detection/semantics) remains independent of any target application.
* **Reuse:** One semantics engine, many **Coder2$** adapters (CAD, DCC, and beyond).
* **Breadth:** **100+ potential software targets** (integrations), far beyond the examples shown here.
* **Maintainability & Scale:** Target-API changes impact only the relevant **Coder2$**, not the core.
* **Portability:** Faster rollout to **new applications**—e.g., sign-language assistance—beyond CAD/DCC.


## 🕹️ Peripherals
| 🧩 **Module** | 📝 **Short Description**                                                                                                                                                                                     | 🔌 **Hardware / Deps**                                                                                                                                                                           | ⚖️ **License** | ⚠️ **Notes**                                                                                                        | 🚦 **Status** | 🔗 **Link** |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------- | ------------------------------------------------------------------------------------------------------------------- | ------------- | ----------- |
| **Pen3D**     | ESP32-S3 BLE pen with **2 buttons / optional scroll wheel**, **dual markers** for optical pose, **haptic motor**, **titanium tube** chassis. Low-latency notifications to host; no IR emitter (camera-safe). | ESP32-S3 module, 2× tact switch or 1× rotary encoder, coin-vibe motor + driver, LiPo 150–300 mAh + charger (MCP73831/TP4056), power switch, passive markers (AprilTag/reflective), Ti tube, PCB. | Apache-2.0     | BLE GATT (notify); deep-sleep wake-on-button; optional USB-CDC debug. Designed to coexist with 850 nm NIR tracking. | 🟡 Planned    | coming soon |


## 🗺️ Roadmap

Coming soon. I’m still in the research phase. 🚀


## ❓ FAQ (Top 10)

#### 1) Can I use a VR headset?
Yes, you can try VR, but the project is **optimized for desktop workflows**. VR is optional and not fully implemented yet; support may be added later.

#### 2) Can 'Meta Quest 3' do 3D hand tracking?
Yes, you can try it, but it’s **not optimal for CAD-grade precision**. Inside-out tracking and 2D→3D lifting are fine for previews/VR UX, but suffer from **occlusion**, **drift**, **no hardware sync/trigger**, and **higher latency**.  
MotionCoder prioritizes **deterministic multi-view 3D capture** (global-shutter/NIR) with **low-latency semantics** for reliable CAD/DCC work.

#### 3) What about LiDAR and ToF sensors?
Useful in constrained scenes, but often limited by **noise**, **low hand resolution**, **latency**, and **vendor lock-in**. We focus on **global-shutter/NIR multi-view** for precision.

#### 4) Why multiple cameras instead of one?
Single-view suffers from **occlusion** and **scale/pose ambiguity**. Multi-view provides **stable 3D geometry**, **less drift**, and **higher intent confidence**—critical for CAD/DCC.

#### 5) Do I need markers or a glove?
No — tracking is **gloveless**. Optional aids (e.g. small **fingertip/nail markers** or a **wrist reference**) and even a **2D touch panel** or fiducials can boost **fine precision** in special setups, but they’re not required.

#### 6) Do I need sign-language skills?
No. Core gestures are **beginner-friendly**. Sign-language nuances are used to improve **expressiveness and disambiguation**, not as a requirement.

#### 7) Privacy & security?
Everything runs **on-premises**. No cloud required. We follow **data minimization**; logs/frames can be disabled or anonymized.

#### 8) Supported apps?
Starting with **Blender** (adapter). Next targets: **Unreal, Unity, Maya, SolidWorks, Rhino/NX**, and other DCC tools. Adapters are modular (`Coder2$`).

#### 9) Will high-quality hardware become cheap?
Costs are trending down. We expect **consumer-priced, higher-quality options** over time, but timelines depend on vendors and supply—no hard promises.

#### 10) Mouse & keyboard?
They stay. Habits don’t change overnight. Cameras mount neatly to the monitor/frame and **don’t get in the way**—MotionCoder **augments** existing input, it doesn’t replace it.


