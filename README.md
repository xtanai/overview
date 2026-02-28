# 🔭 Overview – Real-Time Gesture Interaction for 3D Authoring

## Description

MotionCoder converts raw **sensor streams** into **semantic, low-latency gesture commands** for **CAD/DCC and 3D authoring workflows**. The system is built as a modular stack (**Sensors → AI Interpretation → Connectors**) and is optimized for **stable timing**, **repeatable interactions**, and **professional productivity**—especially in workflows where “flaky” input is unacceptable.

---

## 📚 Documentation & Resources

| 📔 **Name**      | 📝 **Short Description**                                                                                                                                                                               | ⚖️ **License** | 🔗 **Link**                                                  |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------- | ------------------------------------------------------------ |
| **Introduction** | Comprehensive overview of stereo vision fundamentals: what a stereo camera is, how geometry works (baseline, disparity, FOV), and which architectural approach makes sense for different applications. | Apache-2.0     | [Introduction](https://github.com/xtanai/indro) |
| **Sensor Guide** | Sensor selection & integration guide: how to choose the right camera module (MIPI/RAW, global shutter, optics, filters, synchronization, etc.).                                                        | Apache-2.0     | [Sensor](https://github.com/xtanai/sensor-guide)             |
| **Infrared**     | Technical guide to controlled IR illumination: LED/VCSEL selection, driver design, optical filtering (bandpass, polarization), synchronization, and impact on stereo matching stability.               | Apache-2.0     | [Infrared](https://github.com/xtanai/infrared)                     |
| **LuxMeter**     | Practical IR illumination sizing guide without a physical luxmeter: estimate required LED power using RAW10 intensity levels (paper target + fixed camera settings).                                   | Apache-2.0     | [LuxMeter](https://github.com/xtanai/luxmeter)               |
| **GeoRules**     | Practical stereo geometry reference: baseline vs. distance, disparity range planning, accuracy estimation, and CPU workload considerations.                                                            | Apache-2.0     | [Vision Geometry Rules](https://github.com/xtanai/geo_rules) |

---

## 🎥 Layer 1 – Capture

**What this layer does:** This layer **ingests camera/hand-tracking streams**, performs **on-edge preprocessing**, and **synchronizes** frames across devices. It outputs **normalized pose/keypoint streams and ROIs**; if optional AI pipelines are enabled, it can also publish an **H.265 video stream** for preview/segmentation or run **semantic inference directly on the edge** (e.g., via an NPU accelerator).

| 🧩 **Module**      | 📝 **Short Description**                                                                                                                                                      | 🔌 **Hardware / Deps**                                                       | ⚖️ **License** | ⚠️ **Notes**                                                            | 🚦 **Status**         | 🔗 **Link** |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------- | -------------- | ----------------------------------------------------------------------- | --------------------- | ----------- |
| **EdgeTrack**      | **RAW10 mono ingest** on Pi 5; GPU/NEON-optimized preproc                                                                                                                      | **Raspberry Pi 5** (4/8 GB), 2× MIPI-CSI (OV9281 etc.)                       | Apache-2.0     | —                                                                       | 🟡 In progress        | [EdgeTrack](https://github.com/xtanai/edgetrack) |
| **TDMStrobe**      | **Time-Division-Multiplexed IR strobe & camera trigger** (phase control A/B/C/D) for EdgeTrack                                                                                 | IR-LED/VCSEL arrays, LED-drivers, MCU (RP2040)                               | Apache-2.0     | —                                                                       | 🟡 In progress        |  [TDMStrobe](https://github.com/xtanai/tdmstrobe) |
| **EdgeSense**      | AI semantic segmentation & scene understanding on edge (optional pipeline beside geometry-based capture)                                                                       | RGB Camera, optional NPU/AI accelerator                                      | Apache-2.0     | —                                                                       | 🟡 Planned            | coming soon |

---

## 🔗 Layer 2 – Host-side fusion

**What this layer does:** It runs on the host PC and sits between capture and semantics. It **aggregates 2–4 stereo pairs** over LAN, performs **time synchronization**, **multi-view calibration refinement** (incl. bundle adjustment), and **low-latency fusion/filtering** to output **precise, time-consistent 3D key-poses** (joints + confidences + references). The result is a **single canonical pose stream** optimized for deterministic real-time control.

| 🧩 **Module**   | 📝 **Short Description**                                                                                                                                                                                                                                     | 🖥️ **Host**                                                | ⚖️ **License** | ⚠️ **Notes**  | 🚦 **Status** | 🔗 **Link**                                          |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -----------------------------------------------------------|--------------- | -------------- | ------------- | ---------------------------------------------------- |
| **CoreFusion** | Aggregates **2–4 synchronized stereo rigs** over LAN; performs **multi-view calibration**, **bundle adjustment**, **outlier rejection**, and **low-latency fusion** to produce **stable 3D keypoints / keyposes** (joints + confidences + reference anchors). | Host PC (CUDA-capable GPU recommended); ZeroMQ / UDP / TCP | Apache-2.0     | —              | 🟡 Planned    | [CoreFusion](https://github.com/xtanai/corefusion)   |
| **CoreStereo** | Host-side stereo compute layer: ingests **synchronized RAW/rectified stereo frames** from EdgeTrack and performs **disparity / depth reconstruction** (dense or ROI-based), plus optional **filters and confidence maps** for downstream modules.             | High-performance CPU example: AMD Threadripper             | Apache-2.0     | —              | 🟡 Planned    | Coming soon                                          |



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

---

## 🗺️ Roadmap

Coming soon. The project is currently in the research and prototyping phase. 🚀

---
