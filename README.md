# 🔭 Overview – High-Precision Gesture Control for 3D Authoring

## Description

MotionCoder converts raw **sensor streams** into **semantic, low-latency gesture commands** for **CAD/DCC and 3D authoring workflows**. The system is built as a modular stack (**Sensors → AI Interpretation → Connectors**) and is optimized for **stable timing**, **repeatable interactions**, and **professional productivity**—especially in workflows where “flaky” input is unacceptable.

At the moment, no official demo video is available, as the project is still in a very early stage. However, for a first impression of the core idea, you can watch a short [YouTube example](https://www.youtube.com/watch?v=XOO9rzbsx1E) as an early preview.

This prototype demonstrates only a small portion of the planned roadmap and runs on demo-grade hardware. Its primary purpose is to communicate the interaction concept rather than showcase final performance or accuracy. MotionCoder’s goal is to evolve this into a robust, high-precision, editor-focused gesture layer with domain-specific mappings and reliable, deterministic command semantics.

### Layered AI Concept

MotionCoder’s AI is intentionally split into **three layers**, so each stage can use the most suitable model, remain independently upgradable, and integrate cleanly with different sensor setups:

1. **Layer 1 — Geometry & Recognition**
   Converts raw sensor signals into **stable geometric primitives** (e.g., keypoints, pose, markers, tool pose) and performs first-stage **recognition**. **This layer can also work without AI**—for example using deterministic marker/pose geometry and classic vision, with AI being optional for harder cases (occlusions, ambiguous shapes, hand articulation).

2. **Layer 2 — Fusion & Fit Geometry**
   Combines multiple views/devices into a **time-consistent fused state** and applies **fit/constraints** to improve precision, stability, and repeatability.

3. **Layer 3 — Semantics & Command Translation**
   Translates the fused motion/geometry into **high-level, editor-safe gesture semantics**—mapped to reliable commands and domain-specific workflows.

This separation keeps the pipeline **efficient**, makes integration easier across DCC/engines, and allows each AI layer to evolve without breaking the others.

---

## 🎥 Layer 1 – Capture

**What this layer does:** It **ingests camera/IMU/hand-tracker streams**, applies **on-edge preprocessing** (undistort, normalize, optional 2D keypoints), and **synchronizes** frames across devices. Outputs are **normalized pose/keypoint streams** or **compressed video feeds** for triangulation on the host.

| 🧩 **Module**  | 📝 **Short Description**                                                                                                                                                      | 🔌 **Hardware / Deps**                                                       | ⚖️ **License** | ⚠️ **Notes**                                                            | 🚦 **Status**         | 🔗 **Link** |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------- | -------------- | ----------------------------------------------------------------------- | --------------------- | ----------- |
| **EdgeTrack**  | **RAW10 mono ingest** on Pi 5; GPU/NEON-optimized preproc (undistort/normalize), optional on-Pi keypoints; streaming to host for triangulation.                                | **Raspberry Pi 5** (4/8 GB), 2× MIPI-CSI (OV9281 etc.)                       | Apache-2.0     | —                                                                       | 🟡 In progress        | [EdgeTrack](https://github.com/xtanai/edgetrack) |
| **TDMStrobe**  | **Time-Division-Multiplexed IR strobe & camera trigger** (phase control A/B/C/D) for EdgeTrack                                                                                 | IR-LED/VCSEL arrays, LED-drivers, MCU (RP2040)                               | Apache-2.0     | —                                                                       | 🟡 In progress        |  [TDMStrobe](https://github.com/xtanai/tdmstrobe) |

---

## 🔗 Layer 2 – Host-side fusion

**What this layer does:** It runs on the host PC and sits between capture and semantics. It **aggregates 2–4 stereo pairs** over LAN, performs **time synchronization**, **multi-view calibration refinement** (incl. bundle adjustment), and **low-latency fusion/filtering** to output **precise, time-consistent 3D key-poses** (joints + confidences + references). The result is a **single canonical pose stream** optimized for deterministic real-time control.

| 🧩 **Module**   | 📝 **Short Description**                                                                                                                                                                                 | 🖥️ **Host**                                                       | ⚖️ **License** | ⚠️ **Notes**                             | 🚦 **Status** | 🔗 **Link**                                          |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ | -------------- | ------------------------------------------ | ------------- | ---------------------------------------------------- |
| **CoreFusion**  | **Aggregates 2–4 stereo pairs** over LAN; performs **multi-view calibration**, **bundle adjustment**, and **low-latency fusion** to produce **precise 3D key-poses** (joints + confidences + references). | Host PC (CUDA-capable GPU rec.); ZeroMQ, UDP, TCP                  | Apache-2.0     |  —                                         | 🟡 Planned    | [CoreFusion](https://github.com/xtanai/corefusion) |

---

## 🧠 Layer 3 – AI Interpretation 

**What this layer does:** It converts **poses/keypoints** into **high-level intents** using **gesture grammars**, **state machines**, and **context rules** (tool modes, constraints, safety). It handles **debounce**, **disambiguation**, and **confidence scoring**, producing **deterministic, low-latency events**.

| 🧩 **Module**        | 📝 **Short Description**                                 | 🔁 **I/O**                  | ⚖️ **License** | ⚠️ **Notes** | 🚦 **Status**  | 🔗 **Link**                                                                   |
| -------------------- | --------------------------------------------------------- | ---------------------------- | -------------- | ------------ | -------------- | ------------------------------------------------------------------------------ |
| **MotionCoder**      | Real-time gestures/intents, state machine, context logic. | Poses/keypoints from Layer 2 | Apache-2.0     | —            | 🟡 Planned     | [MotionCoder](https://github.com/xtanai/motioncoder) |

---

## 🔌 Layer 4 – Connectors 

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

> Note: These peripherals **don’t require MotionCoder**. They work like **standard input devices** (e.g., HID) and can be used independently.

| 🧩 **Module**   | 📝 **Short Description**                                               | 🔌 **Hardware / Deps**                                                                                            | ⚖️ **License** | ⚠️ **Notes**                                                                                                         | 🚦 **Status** | 🔗 **Link**                                    |
| ---------------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ | -------------- | --------------------------------------------------------------------------------------------------------------------- | ------------- | ---------------------------------------------- | 
| **Pen3D**        | **Tracked 3D pen input with buttons and optional haptics.**            | Optional ESP32-S3 (BLE) or mechanical.                                                                             | Apache-2.0     | BLE GATT (notify); deep-sleep wake-on-button; optional USB-CDC debug. Designed to coexist with 850 nm NIR tracking.   | 🟡 Planned    | [Pen3D](https://github.com/xtanai/pen3d)       |
| **HMDone**       | **Minimal VR headset with external marker-based tracking only.**       | Works with high-resolution HMDs (e.g. Pimax Crystal or Valve). No extra hand controllers required for MotionCoder. | Apache-2.0     | Use with multi-view/NIR rigs for best results; inside-out tracking is intentionally ignored.                          | 🟠 Later      | [HMDone](https://github.com/xtanai/hmdone)     |
| **HandNode**     | **Universal externally tracked hand tool (shape-agnostic).**           | Passive markers with configurable mount geometry; optional BLE button/dial module (ESP32-S3).                      | Apache-2.0     | Visibility-first marker placement; behavior defined by profiles/configuration.                                        | 🟠 Later      | [HandNode](https://github.com/xtanai/handnode) |
     
---

## 📐 Vision Geometry Rules

A concise collection of formulas and reference rules for camera geometry, FOV, and depth precision – see more: 👉 [Vision Geometry Rules](https://github.com/xtanai/geo_rules)

---

## 🗺️ Roadmap

Coming soon. The project is currently in the research and prototyping phase. 🚀

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

### NIR Illumination (850 nm vs 940 nm) & Eye Safety

* **Never look into emitters.** Use black matte **baffles/shields**, aim emitters away from faces, and add **hardware interlocks** (LEDs off on loss of sync, open covers, or presence detection).
* Keep **exposure short** (strobe pulses strictly within camera exposure) and **average irradiance low**.
* Prefer **850 nm band-pass filters** on cameras to reduce the required LED output power.
* **850 nm and 940 nm are both IR-A** and are **not inherently eye-safe**; safety depends on irradiance, geometry, duty cycle, distance, and exposure time (IEC 62471).

### Solution Strategies

* **Option A — Prefer more viewpoints over more power (recommended for 940 nm)**
  If **940 nm illumination** is preferred (reduced visible glow), the recommended approach is to **increase the number of stereo rigs (viewpoints)** to maintain SNR while keeping **irradiance low**, rather than compensating with higher-power NIR emitters.

* **Option B — Side / rear placement (recommended)**
  Mount stereo pairs **left/right and slightly behind** the workspace, aimed toward the work area. Add **one or two top stereo pairs** for occlusion-free coverage. This directs NIR **away from the eyes** while maintaining uniform scene illumination.
Future refinement: recess-mount one stereo pair near the table center and another near the back edge for a slimmer, more robust setup.

* **Option C — Front placement with HMD only**
  If stereo pairs must face forward, operate with a **closed VR headset** (no see-through optics) so the user’s eyes are **occluded**. Baffles and interlocks are still required to protect bystanders.

* **Option D — IR-filtering safety eyewear**
  Use **visible-light-transmitting eyewear** that strongly attenuates **near-IR (≈ 780–950 nm)** (specified optical density at **850 nm / 940 nm**) so users retain normal vision while IR exposure is reduced.

* **Option E — Side-shield eyewear (“horse-blinkers” concept)**
  Provide **IR-blocking safety glasses with side shields** for operators and visitors when emitters face forward. Ensure proper **near-IR attenuation ratings** and a snug fit to block off-axis radiation.

---

### Disclaimer

Prototype hardware. Use at your own risk. Ensure eye‑safety and proper thermal design in all setups.

  
