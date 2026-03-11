# Overview – Precision Gesture Interaction for 3D Creation

## Description

**xtan.ai** explores a new approach to **precision gesture interaction for professional 3D workflows** such as digital content creation (DCC), CAD and virtual production.

Instead of relying purely on AI-based depth estimation, the system is designed around **metric stereo geometry and deterministic tracking pipelines**. The goal is to provide **stable, low-latency spatial interaction** that can be reliably integrated into professional software tools.

The architecture separates the system into several layers: hardware capture, geometric reconstruction, semantic interpretation, and application connectors. Motion data is first captured using stereo vision hardware (such as EdgeTrack rigs), reconstructed into consistent 3D keypoints, and then interpreted by MotionCoder into structured interaction commands.

This modular design allows the same motion interpretation engine to be reused across many applications—from CAD modeling and animation to robotics interfaces and assistive interaction systems.

The project is currently in the **research and prototyping stage**, with a focus on open architecture, reproducibility, and integration with existing professional software ecosystems.

---

# 📚 Documentation & Resources

| 📔 **Name**      | 📝 **Short Description**                                                                              | ⚖️ **License** | 🔗 **Link**                                             |
| ---------------- | ----------------------------------------------------------------------------------------------------- | -------------- | ------------------------------------------------------- |
| **Introduction** | Overview of the interaction concept, MotionCoder architecture, and supported hardware configurations. | Apache-2.0     | [Introduction](./docs/intro.md) |

---

# 🧠 Motion Interpretation

**What this layer does:**
It converts **poses and keypoints** into **high-level intents** using **gesture grammars**, **state machines**, and **context rules** (tool modes, constraints, safety).

The layer handles:

* debounce
* disambiguation
* confidence scoring
* temporal consistency

The result is a set of **deterministic, low-latency interaction events** suitable for professional applications.

| 🧩 **Module**   | 📝 **Short Description**                                                      | 🔁 **I/O**                     | ⚖️ **License** | ⚠️ **Notes** | 🚦 **Status** | 🔗 **Link**                                          |
| --------------- | ----------------------------------------------------------------------------- | ------------------------------ | -------------- | ------------ | ------------- | ---------------------------------------------------- |
| **MotionCoder** | Real-time gesture interpretation engine with state machine and context logic. | Poses/keypoints from EdgeTrack | Apache-2.0     | —            | 🟡 Planned    | [MotionCoder](https://github.com/xtanai/motioncoder) |

---

# 🎥 Hardware Recommendation

For high-precision tracking, the system can be combined with **EdgeTrack stereo rigs**, which provide synchronized NIR stereo capture and geometry-based depth reconstruction.

More information about the hardware layer can be found here:

* [https://edgetrack.org](https://edgetrack.org)
* [https://github.com/edgetrackorg/overview](https://github.com/edgetrackorg/overview)

---

# 🔌 Connectors

**What this layer does:**
It maps **interaction intents from MotionCoder** to **application-native actions** such as operators, hotkeys, API calls, or engine events.

Each connector module (`Coder2$`) targets a specific software ecosystem and translates MotionCoder output into application commands.

| 🧩 **Module**      | 📝 **Short Description**                                 | 📲 **Target System**        | ⚖️ **License** | ⚠️ **Notes** | 🚦 **Status**                 | 🔗 **Link** |
| ------------------ | -------------------------------------------------------- | --------------------------- | -------------- | ------------ | ----------------------------- | ----------- |
| **Coder2Blender**  | Add-on/API bridge: gestures → operators, hotkeys, nodes. | Blender                     | MIT            | —            | 🟡 Research (API exploration) | coming soon |
| **Coder2Unreal**   | Plugin bridge: gestures → Blueprint/C++ events.          | Unreal Engine               | MIT            | —            | 🟡 Planned                    | coming soon |
| **Coder2Dassault** | Macro/API bridge: gestures → CAD commands.               | Dassault (SolidWorks/CATIA) | MIT            | —            | 🟠 Targeted for next year     | coming soon |

### Why separate MotionCoder and Coder2$?

**Modularity**
MotionCoder (gesture detection and semantics) remains independent from any target application.

**Reuse**
One interpretation engine can support many software integrations.

**Breadth**
Potentially **100+ software targets** (CAD, DCC, robotics tools, assistive interfaces).

**Maintainability**
API changes affect only the relevant adapter, not the core engine.

**Portability**
Enables fast integration into new software ecosystems.

---

# 🕹️ Peripherals (optional)

This layer includes optional hardware devices designed to **improve ergonomics and interaction precision**.

Examples include:

* clutch/confirm buttons
* mode switches
* haptic feedback

These devices communicate via **BLE or USB** and are designed to **avoid NIR interference**, ensuring compatibility with camera-based tracking systems.

> Note: These peripherals **do not require MotionCoder** and can operate as standard input devices (HID).

| 🧩 **Module** | 📝 **Short Description**                                              | 🔌 **Hardware / Dependencies**                                        | ⚖️ **License** | ⚠️ **Notes**                                                                                                     | 🚦 **Status** | 🔗 **Link**                                |
| ------------- | --------------------------------------------------------------------- | --------------------------------------------------------------------- | -------------- | ---------------------------------------------------------------------------------------------------------------- | ------------- | ------------------------------------------ |
| **Pen3D**     | Tracked 3D pen with buttons and optional haptic feedback.             | Optional ESP32-S3 (BLE) or mechanical design.                         | Apache-2.0     | BLE GATT notifications, deep-sleep wake-on-button, optional USB-CDC debug. Designed for 850 nm NIR environments. | 🟡 Planned    | [Pen3D](https://github.com/xtanai/pen3d)   |
| **HMDone**    | Minimal VR headset concept using external marker-based tracking only. | Works with high-resolution HMDs (e.g. Pimax Crystal, Valve headsets). | Apache-2.0     | Designed for multi-view NIR tracking. Inside-out tracking intentionally ignored.                                 | 🟠 Later      | [HMDone](https://github.com/xtanai/hmdone) |

---

# 🗺️ Roadmap

Coming soon.

The project is currently in the **research and prototyping phase**, focusing on:

* architecture definition
* stereo-based tracking experiments
* gesture interaction models
* early software integrations

More details will be published as the ecosystem evolves. 🚀