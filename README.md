# 🔭 Overview – AI Gesture Control for 3D Workflows

## Description

MotionCoder converts raw **sensor streams** into **semantic, low-latency gestures** and delivers them to your **CAD/DCC tools** through lightweight connectors. Built as a **modular stack** (Sensors → AI Interpretation → Connectors), it is tuned for **deterministic, high-precision workflows**—achieving **greater accuracy** than typical inside-out tracking or controller solutions (e.g., **Leap Motion**, **Kinect**, **Meta Quest 3** or **HTC Vive**), yet portable across domains.

For a quick preview, here’s a short [YouTube example](https://www.youtube.com/watch?v=923FFy5cI-4). It demonstrates only **~2–3%** of the intended capability and runs on non-precision demo hardware **Leap Motion old version**. MotionCoder’s goal is **full to 100%, high-accuracy gesture coverage**, informed by my hands-on experience in gesture design and control.

---

## 🎥 Layer 1 – Sensor I/O

**What this layer does:** It **ingests camera/IMU/hand-tracker streams**, applies **on-edge preprocessing** (undistort, normalize, optional 2D keypoints), and **synchronizes** frames across devices. Outputs are **normalized pose/keypoint streams** or **compressed video feeds** for triangulation on the host.

*Note: Choose the module based on your budget! More in the GitHub docs: 👉 [Sensor Guide](https://github.com/xtanai/sensor-guide).*

*Recommendation for a fast, affordable start:* **EdgeTrack** + **TDMStrobe** + **CoreFusion**.

| 🧩 **Module**  | 📝 **Short Description**                                                                                                                                                      | 🔌 **Hardware / Deps**                                                       | ⚖️ **License** | ⚠️ **Notes**                                                            | 🚦 **Status**         | 🔗 **Link** |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------- | -------------- | ----------------------------------------------------------------------- | --------------------- | ----------- |
| **EdgeTrack**  | **RAW10 mono ingest** on Pi 5; GPU/NEON-optimized preproc (undistort/normalize), optional on-Pi keypoints; streaming to host for triangulation.                                | **Raspberry Pi 5** (4/8 GB), 2× MIPI-CSI (OV9281 etc.)                       | Apache-2.0     | —                                                                       | 🟡 Planned            | [EdgeTrack](https://github.com/xtanai/edgetrack) |
| **MVCore3D**   | **MJPEG ingest** (libjpeg-turbo), triangulation via **Anipose**, 2D keypoints via **MMPose**; **CPU-tuned** plug-and-play for multi-cam.                                       | USB-UVC cams (incl. low-cost), optional sync                                 | Apache-2.0     | —                                                                       | 🟠 Later              | coming soon |
| **MVYUV3D**    | **Uncompressed YUV ingest** (**YUY2/UYVY 4:2:2**, optional **NV12 4:2:0**); lower CPU load than MJPEG, higher USB load than MONO8; triangulation **Anipose**, pose **MMPose**. | UVC cams with YUV output; stable lighting; optional soft/hard sync           | Apache-2.0     | —                                                                       | 🟠 Later              | coming soon |
| **MVRAW3D**    | **RAW10/12 (Bayer/Mono) ingest**, debayer/denoise pipeline; **lower latency & higher fidelity** vs. MJPEG; GPU-accelerated where available.                                    | Global-/rolling-shutter cams; **HW sync recommended**                        | Apache-2.0     | —                                                                       | 🟠 Later              | coming soon |
| **MVMono3D**   | **Synchronized mono multi-cam** (global shutter) with high occlusion robustness & precision; only RAW12 for GPUDirect.                                                         | 3–4 mono cams only RAW12 with Interface **SFP+** or **CoaXPress**            | Apache-2.0     | —                                                                       | 🟠 Targeted next year | coming soon |
| **TDMStrobe**  | **Time-Division-Multiplexed IR strobe & camera trigger** (phase control A/B/C/D) for MV* pipelines and EdgeTrack                                                               | IR-LED/VCSEL arrays, MOSFET drivers, MCU (ESP32)                             | Apache-2.0     | —                                                                       | 🟡 Planned            |  [TDMStrobe](https://github.com/xtanai/tdmstrobe) |
| **Leap2Pose**  | **LeapC ingestion → normalized poses/streams** (consistent joints & units) for downstream adapters.                                                                            | Leap Motion Controller 1/2                                                   | MIT            | Not recommended for production – many limitations, therefore deprecated | 🔴 Canceled           | none |

---

## Layer 2 – Host-side fusion (between Sensor I/O and Host)

**What this layer does:** It runs on the host PC and sits between capture and semantics. It **aggregates 2–4 stereo pairs** over LAN, performs **time synchronization**, **multi-view calibration refinement** (incl. bundle adjustment), and **low-latency fusion/filtering** to output **precise, time-consistent 3D key-poses** (joints + confidences + references). The result is a **single canonical pose stream** optimized for deterministic real-time control.

| 🧩 **Module**   | 📝 **Short Description**                                                                                                                                                                                 | 🖥️ **Host Hardware**                                                 | ⚖️ **License** | ⚠️ **Notes**                             | 🚦 **Status** | 🔗 **Link**                                          |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ | -------------- | ------------------------------------------ | ------------- | ---------------------------------------------------- |
| **CoreFusion**  | **Aggregates 2–4 stereo pairs** over LAN; performs **multi-view calibration**, **bundle adjustment**, and **low-latency fusion** to produce **precise 3D key-poses** (joints + confidences + references). | Host PC (CUDA-capable GPU rec.); ZeroMQ, UDP, TCP                  | Apache-2.0     |  —                                         | 🟡 Planned    | [CoreFusion](https://github.com/xtanai/corefusion) |

---

## 🧠 Layer 3 – AI Interpretation (Pose → Intents/Gestures)

**What this layer does:** It converts **poses/keypoints** into **high-level intents** using **gesture grammars**, **state machines**, and **context rules** (tool modes, constraints, safety). It handles **debounce**, **disambiguation**, and **confidence scoring**, producing **deterministic, low-latency events**.

| 🧩 **Module**        | 📝 **Short Description**                                 | 🔁 **I/O**                  | ⚖️ **License** | ⚠️ **Notes** | 🚦 **Status**  | 🔗 **Link**                                                                   |
| -------------------- | --------------------------------------------------------- | ---------------------------- | -------------- | ------------ | -------------- | ------------------------------------------------------------------------------ |
| **MotionCoder**      | Real-time gestures/intents, state machine, context logic. | Poses/keypoints from Layer 1 | Apache-2.0     | —            | 🟡 In progress | [MotionCoder](https://github.com/xtanai/motioncoder) |
| **Pose2Gizmo**       | Poses → 3D manipulator/gizmo commands & visualization.    | Normalized poses, tool hints | MIT            | —            | 🟡 Planned     | coming soon   |

---

## 🔗 Layer 4 – Connectors (DCC/CAD/Engines)

**What this layer does:** It maps **intents** from MotionCoder to **app-native actions** (operators, hotkeys, API calls, Blueprint/C++ events), with **non-intrusive adapters** that track upstream API changes. Each `Coder2$` module targets one ecosystem.

| 🧩 **Module**           | 📝 **Short Description**                               | 📲 **Target System** | ⚖️ **License** | ⚠️ **Notes** | 🚦 **Status**                 | 🔗 **Link**                                                                         |
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

---

## 🕹️ Peripherals

**What this layer does:** Purpose-built devices that **improve ergonomics and precision** (e.g., clutch/confirm, mode switches, haptic cues). They speak **BLE/USB** and avoid IR emission to stay **camera-safe** in NIR setups.

| 🧩 **Module** | 📝 **Short Description**                                                                                                                                                                                                                                                         | 🔌 **Hardware / Deps**                                                                                                                                                                           | ⚖️ **License** | ⚠️ **Notes**                                                                                                       | 🚦 **Status** | 🔗 **Link** |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------------------------------------------------------------------------------------------------------------------- | ------------- | ----------- |
| **Pen3D**     | **2 buttons + optional scroll wheel**, **dual optical markers** for precise pose, **haptic motor**, **titanium tube** chassis. Optional **left-hand companion** with **mini-joystick** and **scroll wheel**. **Low-latency host notifications**; **no IR emitter** (camera-safe). | ESP32-S3 module, 2× tact switch, 1× rotary encoder, coin-vibe motor + driver, LiPo 150–300 mAh + charger (MCP73831/TP4056), power switch, passive markers (AprilTag/reflective), Ti tube, PCB.    | Apache-2.0     | BLE GATT (notify); deep-sleep wake-on-button; optional USB-CDC debug. Designed to coexist with 850 nm NIR tracking. | 🟡 Planned    | coming soon |
| **VRHeadSet** | Prefer **marker-based tracking** on the headset (e.g., 3–4 passive markers) with **cameras external to the HMD** for higher precision; **disable/ignore built-in inside-out hand tracking** for CAD-grade work.                                                                   | Works with high-res HMDs (e.g., Pimax Crystal). **No extra hand controllers required** for MotionCoder.                                                                                           | Apache-2.0     | Use with multi-view/NIR rigs for best results; inside-out alone is not sufficient for precision CAD gestures.       | 🟠 Later      | coming soon |

---

## 📐 Vision Geometry Rules

A concise collection of formulas and reference rules for camera geometry, FOV, and depth precision – see more: 👉 [Vision Geometry Rules](https://github.com/xtanai/geo_rules)

---

## 🗺️ Roadmap

Coming soon. I’m still in the research phase. 🚀

---

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

---

## Safety

* **850 nm IR is invisible and hazardous**—follow **IEC 62471** guidelines.
* **Never look into emitters.** Use black matte **baffles/shields**, aim emitters away from faces, and add **interlocks** (LEDs off on loss of sync / open cover / presence detection).
* Keep **exposure short** (strobe pulses inside the camera exposure) and the **average power low**.
* Use **850 nm band-pass filters** on cameras to reduce required LED power.

**Solution Strategies:**

* **Option A – Side/Rear placement (recommended):**
  Mount stereo pairs **left/right and slightly behind** the workspace, aimed toward the monitor/work area. Add **one or two top stereo pairs** for occlusion-free coverage. This directs IR **away from eyes** while keeping the scene well lit. Future refinement: integrate cameras cleanly by recess-mounting one pair near the center of the table and another near the back edge for a slimmer, more robust design.

* **Option B – Front placement with HMD:**
  If all stereo pairs must face forward, operate with a **closed VR headset** (no see-through optics) so eyes are **occluded**. Still use baffles and interlocks to protect bystanders without headsets.

* **Option C – IR-filtering safety glasses:**
  Use **visible-light-transmitting eyewear** that strongly attenuates **near-IR (≈ 780–950 nm)** (e.g., specified optical density at **850 nm**), so users retain normal vision while IR exposure is reduced.

* **Option D – Side-shield eyewear (“horse blinkers” idea):**
  Provide **IR-blocking safety glasses with side shields** for operators/visitors when emitters face forward. Choose eyewear rated for **near-IR attenuation** and ensure a snug fit to block off-axis light.

**Note:** 

For high-precision CAD/DCC tracking, **multi-view NIR with global-shutter cameras and synchronized strobes** typically outperforms inside-out approaches (e.g., consumer hand-tracking like **Leap Motion** or **ToF** controllers) in **accuracy, drift resistance, and latency**—making it a uniquely capable solution compared with devices such as the **Meta Quest 3** and similar systems.

### Disclaimer

Prototype hardware. Use at your own risk. Ensure eye‑safety and proper thermal design in all setups.

  
