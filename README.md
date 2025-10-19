# 🔭 Overview – Hand-Tracking 3D Workflows

## 🧷 Layer 1 – Sensor I/O

| 🧩 **Module**       | 📝 **Short Description**                                          | 🔌 **Hardware/Dependencies**               | ⚖️ **License** | ⚠️ **Notes** | 🚦 **Status**            | 🔗 **Link**                                                                |
| ------------------- | ------------------------------------------------------------------ | ------------------------------------------- | -------------- | ------------ | ------------------------- | -------------------------------------------------------------------------- |
| **MVCore3D**        | Anipose + MMPose stack with CPU tuning for optimal performance.    | Various cameras (incl. low-cost), sync, PTZ | Apache-2.0     | —            | 🟡 Planned                | [MVCore3D](https://github.com/xtanai/mvcore3d)   |
| **Leap2Pose**       | **LeapC** ingestion → normalized poses/streams.                    | Leap Motion (Controller 1/2)                | MIT            | —            | 🟢 Active                 | [Leap2Pose](https://github.com/xtanai/leap2pose) |
| **MVMono3D**        | Mono multi-camera pipeline (high perf, high occlusion robustness). | 3–4 mono cams, IR/filter, sync              | Apache-2.0     | —            | 🟠 Targeted for next year | [MVMono3D](https://github.com/xtanai/mvmono3d)   |
| **TDMStrobe**       | Time-Division-Multiplexed IR strobe + camera trigger               | IR-LED arrays, MOSFET drivers, MCU          | Apache-2.0     | —            | 🟡 Planned                | [TDMStrobe](https://github.com/xtanai/tdmstrobe)   |

## 🧠 Layer 2 – AI Interpretation (Pose → Intents/Gestures)

| 🧩 **Module**        | 📝 **Short Description**                                 | 🔁 **I/O**                  | ⚖️ **License** | ⚠️ **Notes** | 🚦 **Status**  | 🔗 **Link**                                                                   |
| -------------------- | --------------------------------------------------------- | ---------------------------- | -------------- | ------------ | -------------- | ------------------------------------------------------------------------------ |
| **MotionCoder**      | Real-time gestures/intents, state machine, context logic. | Poses/keypoints from Layer 1 | Apache-2.0     | —            | 🟡 In progress | [MotionCoder](https://github.com/xtanai/motioncoder) |
| **Pose2Gizmo**       | Poses → 3D manipulator/gizmo commands & visualization.    | Normalized poses, tool hints | MIT            | —            | 🟡 Planned     | [Pose2Gizmo](https://github.com/xtanai/pose2gizmo)   |

## 🔗 Layer 3 – Connectors (DCC/CAD/Engines)

| 🧩 **Module**           | 📝 **Short Description**                               | 🖥️ **Target System** | ⚖️ **License** | ⚠️ **Notes** | 🚦 **Status**                 | 🔗 **Link**                                                                         |
| ------------------------ | ------------------------------------------------------ | --------------------- | -------------- | ------------ | ----------------------------- | ------------------------------------------------------------------------------------ |
| **Coder2Blender**        | Add-on/API bridge: gestures → operators/hotkeys/nodes. | Blender               | MIT            | —            | 🟡 Research (API exploration) | [Coder2Blender](https://github.com/xtanai/coder2blender)   |
| **Coder2Unreal**         | Plugin/bridge: gestures → Blueprint/C++ events.        | Unreal Engine         | MIT            | —            | 🟡 Planned                    | [Coder2Unreal](https://github.com/xtanai/coder2unreal)     |
| **Coder2Dassault**       | Macro/API bridge: gestures → CAD commands.             | Dassault (SWX/CATIA)  | MIT            | —            | 🟠 Targeted for next year     | [Coder2Dassault](https://github.com/xtanai/coder2dassault) |

## 🗺️ Roadmap

Coming soon. 🚀
