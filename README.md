# 🧭 Multi-Agent Cooperative Mapping and Autonomous Navigation (NavSLAM System)

---

## 📘 Overview

This project implements a **two-phase environmental mapping and routing system** that combines **SLAM (Simultaneous Localization and Mapping)**, **optical marker-based localization**, and **A\*** path planning.  

The pipeline coordinates a two-stage terrestrial robotic workflow:
- 🛒 **Sensing Node (Mavic)** — utilized in *Phase 1* for structural environmental scanning and optical marker detection.  
- 🤖 **Mobile Platform (TurtleBot3)** — utilized in *Phase 2* for closed-loop indoor navigation relying strictly on local computer vision, LiDAR, and localized coordinate updates.

---

## 🏗️ Phase 1 — Environment Mapping & Marker Localization

**Goal:** Construct a LiDAR-based floor plan of a multi-room indoor facility and register the global coordinates of spatial landmarks (QR markers).

### Key Features
- Commanded the **mobile sensing unit** to explore a layout containing **6–7 distinct zones**.
- Positioned unique **QR markers** at entry points to serve as absolute structural anchor points.
- Embedded a real-time parsing controller to decode each marker's:
  - Numeric Identifier (ID)
  - Extracted 2D coordinate vector
  - Spatial orientation matrix
- Persisted all structural landmark data into a structured **`qr_positions.json`** file.
- Utilized a **continuous rotating LiDAR assembly** to gather 360-degree point-cloud data efficiently without disrupting tracking paths.
- Leveraged localized coordinate logging to guarantee:
  - High-fidelity floor plan generation  
  - High-precision landmark registration

### Outcome
At the completion of Phase 1:
- A complete **LiDAR-based floor plan** of the indoor environment is generated.
- A **JSON layout file** establishes the reference database of every landmark's global coordinates.
- This structural database serves as the geometric baseline for Phase 2.

---

## 🧭 Phase 2 — Autonomous Indoor Routing

**Goal:** Execute precise dead-reckoning navigation between localized zones using computer vision, short-range LiDAR, and the pre-mapped landmark database.

### Technical Approach

1. **Initial Point-of-Interest Detection**
   - The mobile robotic platform performs an in-place rotation, utilizing HSV color segmentation to isolate and align with designated marker frames.
   - Once the visual feature is locked, the platform centers the object within the camera's field of view to establish its trajectory.

2. **Optical Marker Processing for Position Estimation**
   - As the platform advances toward the entry point, the optical marker enters the camera frame.
   - The system decodes the marker ID and queries the **landmark database** compiled during Phase 1.
   - This provides an initial, bounded approximation of the vehicle's position within the grid.

3. **Pose Refinement Loop**
   - When the marker exits the visual frame, a dedicated **state-estimation timer (≈4 seconds)** is initialized.
   - Upon timer expiration, the vehicle reaches the deterministic center of the entry junction.
   - The vehicle's internal pose estimator is instantly updated to match the highly accurate coordinate map, achieving a **3–4 cm localization accuracy** across validation trials.

4. **Algorithmic Path Planning**
   - Utilizing the refined pose vector, a localized **A\*** algorithm computes an optimal, obstacle-free path to the destination coordinate.
   - The generated waypoints are fed into the low-level motor controllers for sequential execution until the target coordinate is reached.

---

## 🎬 System Demonstrations

### 🗺️ Phase 1 — Environment Mapping Stack
- Robotic sensing unit executes autonomous spatial scanning.
- Populates the localized reference database.
- [Mapping Verification Video](https://github.com/JZX100II/Drone2Bot-Navigation-System/blob/main/Recordings%20and%20Figures/Mavic.mp4)

---

### 🤖 Phase 2 — Autonomous Floor Platform Routing
- Performs feature alignment, processes optical markers, and generates global trajectories.
- [Mobile Platform Navigation Video](https://github.com/JZX100II/Drone2Bot-Navigation-System/blob/main/Recordings%20and%20Figures/Turtle.mp4)
