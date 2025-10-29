# 🔭 Overview – AI Gesture Control for 3D Workflows

**Description:**
MotionCoder turns **sensor streams** into **semantic gestures/intents** in real time, then routes them to your **target software** via lightweight connectors.
The stack is **modular** (Sensors → AI Interpretation → Connectors), **low-latency**, and optimized for **CAD/DCC**—yet portable to many other domains.

## 🎥 Layer 1 – Sensor I/O

| 🧩 **Module**  | 📝 **Short Description**                                                                                                                                                         | 🔌 **Hardware / Deps**                                                    | ⚖️ **License** | ⚠️ **Notes**                                                             | 🚦 **Status**         | 🔗 **Link** |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- | -------------- | ------------------------------------------------------------------------ | --------------------- | ----------- |
| **Pi5Track3D** | **RAW10 mono ingest** auf Pi 5; GPU-/NEON-optimierte Preproc (undistort/normalize), optional On-Pi Keypoints; Stream an Host für Triangulation.                                  | **Raspberry Pi 5** (4/8 GB), 2× MIPI-CSI (OV9281 o. ä.), NVMe/USB-2.5 GbE | Apache-2.0     | Fix exposure/gain; HW-Trigger empfohlen; Jumbo MTU für Streaming.        | 🟡 Planned            | coming soon |
| **MVCore3D**   | **MJPEG ingest** (libjpeg-turbo), Triangulation via **Anipose**, 2D-Keypoints via **MMPose**; **CPU-tuned** Plug-&-Play für Multi-Cam.                                           | USB-UVC Cams (auch Low-Cost), optional Sync                               | Apache-2.0     | Höhere CPU-Last durch Decode; deterministisch bei festen Auto-Settings.  | 🟠 later            | coming soon |
| **MVYUV3D**    | **Uncompressed YUV ingest** (**YUY2/UYVY 4:2:2**, optional **NV12 4:2:0**); geringere CPU-Last als MJPEG, höhere USB-Last als MONO8; Triangulation **Anipose**, Pose **MMPose**. | UVC-Cams mit YUV-Output; stabile Beleuchtung; Soft/Hard-Sync optional     | Apache-2.0     | **libjpeg-turbo** nicht nötig; Auto-Funktionen aus ⇒ mehr Determinismus. | 🟠 later            | coming soon |
| **MVRaw3D**    | **RAW10/12 (Bayer/Mono) ingest**, Debayer/Denoise-Pipeline; **niedrige Latenz & hohe Fidelity** gegenüber MJPEG; GPU-Beschleunigung wo möglich.                                  | Global-/Rolling-Shutter Cams; **HW-Sync empfohlen**                       | Apache-2.0     | Exakte Lens-/Noise-Parameter verbessern Keypoints deutlich.              | 🟠 later            | coming soon |
| **Leap2Pose**  | **LeapC ingestion → normalisierte Posen/Streams** (einheitliche Joints/Units) für Downstream-Adapter.                                                                            | Leap Motion Controller 1/2                                                | MIT            | Desktop-first; VR optional.                                              | 🟢 Active             | coming soon |
| **MVMono3D**   | **Synchrones Mono-Multi-Cam** (Global Shutter) mit hoher Okklusions-Robustheit & Präzision; NIR-tauglich.                                                                        | 3–4 Mono-Cams, **IR + Bandpass**, **HW-Trigger/Sync**                     | Apache-2.0     | Kurze Belichtung (0.3–0.8 ms), fester Gain.                              | 🟠 Targeted next year | coming soon |
| **TDMStrobe**  | **Time-Division-Multiplexed IR-Strobe & Camera-Trigger** (Phasensteuerung A/B/C/D) für MV*-Pipelines.                                                                            | IR-LED/VCSEL Arrays, MOSFET-Treiber, MCU                                  | Apache-2.0     | Reduziert IR-Crosstalk; **850 nm Bandpass** empfohlen.                   | 🟠 later            | coming soon |




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



## 🎥 Recommended Sensors

Choose from three sensor modules — **MVCore3D**, **Leap2Pose**, and **MVMono3D** — based on your **budget** and target **quality**, with matching **hardware recommendations**. More in the GitHub docs: 👉 [Sensor Guide](https://github.com/xtanai/sensor-guide)


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
No—tracking is **markerless**. Optional aids (e.g., a **2D touch panel** or fiducials) can boost **fine precision** in special setups, but aren’t required.

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


