# 🧭 Multi-Agent Cooperative Mapping and Autonomous Navigation (NavSLAM System)

---

## Overview

This project implements a cooperative two-robot system for environmental mapping and autonomous navigation. The mapping robot uses GPS, a compass, and LiDAR to create a map of the environment and record landmark coordinates. The mobile robot receives this map and uses computer vision, QR markers, and motion-based pose updates to localize itself and navigate toward a target using A* path planning.

---

## 🏗️ Phase 1 — Environment Mapping & Marker Localization

**Goal:** Construct a LiDAR-based floor plan of a multi-room indoor facility and register the coordinates of landmarks in the environment (QR markers).

### Key Features
- Commanded the mobile sensing unit to explore a layout containing 6–7 distinct zones.
- Positioned unique QR markers at entry points to serve as reference points.
- Implemented a controller to read the following data from each marker in real time:
  - Numeric Identifier (ID)
  - Extracted 2D coordinate vector
  - Spatial orientation matrix
- Used a continuously rotating LiDAR to collect 360-degree data as the robot moved through the environment.
- Leveraged localized coordinate logging to support:
  - Floor plan generation  
  - Landmark registration

### Outcome
At the completion of Phase 1:
- A LiDAR-based floor plan of the indoor environment is generated.
- This structural database serves as the map used in Phase 2.

---

## 🧭 Phase 2 — Autonomous Indoor Routing

**Goal:** Navigate between mapped locations using visual localization and motion-based pose updates.

### Technical Approach

1. **Initial Point-of-Interest Detection**
   - The mobile robotic platform performs an in-place rotation, utilizing HSV color segmentation to isolate and align with designated marker frames.
   - Once the visual feature is locked, the platform centers the object within the camera's field of view to establish its trajectory.

2. **Optical Marker Processing for Position Estimation**
   - As the platform advances toward the entry point, the optical marker enters the camera frame.
   - The system decodes the marker ID and queries the landmark database compiled during Phase 1.
   - This provides an initial estimate of the robot's position within the mapped environment.

3. **Pose Refinement Loop**
   - When the marker exits the visual frame, a dedicated state-estimation timer (≈4 seconds) is initialized.
   - Upon timer expiration, the vehicle reaches the expected position at the entry junction after the timed movement.
   - The vehicle's internal pose estimate is updated using the mapped coordinates of the entry junction, helping correct errors in the robot's estimated position before path planning.

4. **Algorithmic Path Planning**
   - Using the refined pose estimate, the A* algorithm computes a path to the destination on the mapped environment.
   - The generated waypoints are fed into the low-level motor controllers for sequential execution until the target coordinate is reached.

---

## 🎬 System Demonstrations

### 🗺️ Phase 1 — Environment Mapping Stack
- Robotic sensing unit executes autonomous spatial scanning.
- Populates the localized reference database.
- [Mapping Verification Video](https://github.com/JZX100II/Multi-Agent-Indoor-Navigation/blob/main/Recordings%20and%20Figures/Mavic.mp4)

---

### 🤖 Phase 2 — Autonomous Floor Platform Routing
- Performs feature alignment, processes optical markers, and generates global trajectories.
- [Mobile Platform Navigation Video](https://github.com/JZX100II/Multi-Agent-Indoor-Navigation/blob/main/Recordings%20and%20Figures/Turtle.mp4)
