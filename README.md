# 🔭 Overview – AI Gesture Control for 3D Workflows

**Description:**
MotionCoder turns **sensor streams** into **semantic gestures/intents** in real time, then routes them to your **target software** via lightweight connectors.
The stack is **modular** (Sensors → AI Interpretation → Connectors), **low-latency**, and optimized for **CAD/DCC**—yet portable to many other domains.

## 🎥 Layer 1 – Sensor I/O

| 🧩 **Module**       | 📝 **Short Description**                                          | 🔌 **Hardware/Dependencies**               | ⚖️ **License** | ⚠️ **Notes**                             | 🚦 **Status**            | 🔗 **Link**                                                                |
| ------------------- | ------------------------------------------------------------------ | ------------------------------------------- | -------------- | ----------------------------------------- | ------------------------- | -------------------------------------------------------------------------- |
| **MVCore3D**        | Anipose + MMPose stack with CPU tuning for optimal performance.    | Various cameras (incl. low-cost), sync, PTZ | Apache-2.0     | —                                         | 🟡 Planned                | coming soon   |
| **Leap2Pose**       | **LeapC** ingestion → normalized poses/streams.                    | Leap Motion (Controller 1/2)                | MIT            | —                                         | 🟢 Active                 | coming soon |
| **MVMono3D**        | Mono multi-camera pipeline (high perf, high occlusion robustness). | 3–4 mono cams, IR/filter, sync              | Apache-2.0     | —                                         | 🟠 Targeted for next year | coming soon  |
| **TDMStrobe**       | Time-Division-Multiplexed IR strobe + camera trigger               | IR-LED arrays, MOSFET drivers, MCU          | Apache-2.0     | Designed for **MVCore3D** & **MVMono3D**  | 🟡 Planned                | coming soon   |

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


**Why separate (MotionCoder ↔ Coder2#)?**

* **Modularity:** MotionCoder (detection/semantics) remains independent of any target application.
* **Reuse:** One semantics engine, many **Coder2#** adapters (CAD, DCC, and beyond).
* **Breadth:** **100+ potential software targets** (integrations), far beyond the examples shown here.
* **Maintainability & Scale:** Target-API changes impact only the relevant **Coder2#**, not the core.
* **Portability:** Faster rollout to **new applications**—e.g., sign-language assistance—beyond CAD/DCC.



## 🎥 Recommended Sensors

Choose from three sensor modules — **MVCore3D**, **Leap2Pose**, and **MVMono3D** — based on your **budget** and target **quality**, with matching **hardware recommendations**. More in the GitHub docs: 👉 [Sensor Guide](https://github.com/xtanai/sensor-guide)


## 🗺️ Roadmap

Coming soon. 🚀
