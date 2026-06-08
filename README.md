# Autonomous Target Tracking and Visual Servo Control System for UAVs Using YOLOv4

This repository contains the source code for our **University Graduation Project (畢業專題)**. It features an autonomous object-tracking and closed-loop control system developed for the **DJI Tello** drone, powered by the **YOLOv4 (Darknet)** deep learning framework and OpenCV.

---

## 👥 Project Information

* **Institution:** [Your University Name] (你的大學名稱)
* **Department:** [Your Department Name] (你的科系名稱)
* **Project Members:** [Member 1], [Member 2] (專題組員姓名)
* **Advisor:** Prof. [Advisor Name] (指導教授姓名)

---

## 📌 Project Overview & Motivation

Autonomous Unmanned Aerial Vehicles (UAVs) often require robust vision systems to interact with their environment. The objective of this project is to implement a **Visual Servo Control System** that enables a commercial drone (DJI Tello) to lock onto and autonomously follow a specific target without manual pilot intervention.

By utilizing **YOLOv4**, the system achieves low-latency target detection. The calculated bounding box coordinates are then fed into a bounding-box-filtering algorithm to map out control errors, sending flight commands dynamically to maintain the target at the center of the drone's field of view (FOV).

---

## ⚙️ System Architecture

To guarantee smooth live streaming ($960 \times 720$ resolution) and prevent control signal lag, the software architecture utilizes **Multi-threaded Concurrency** and **Thread-safe Queues**:

1.  **Video Capture Thread:** Streams live RGB frames from the DJI Tello onboard camera.
2.  **AI Inference Thread:** Feeds processed frames into the YOLOv4 Darknet neural network to extract bounding boxes ($x, y, w, h$).
3.  **Visual Servo Control Thread:** Calculates the tracking errors based on the target center relative to a pre-defined central safety zone and dispatches flight commands.

---

## ⌨️ Control Logic & Boundaries

### 1. Autonomous Tracking Mode (Closed-Loop)
The system establishes a central green boundary box ($420 \le x \le 540$, $175 \le y \le 295$) on the $960 \times 720$ resolution frame. 

* **Target in Center:** Triggers `move_forward(180)` to approach the target.
* **Target Deviated Up / Down:** Dispatches vertical correction commands (`move_up` / `move_down`).
* **Target Deviated Left / Right:** Dispatches horizontal correction commands (`move_left` / `move_right`).

*Note: A 3-second control throttle is implemented (`(time.time() - t0) % 3 > 2.95`) to prevent flooding the Tello drone with overlapping commands.*

### 2. Manual Keyboard Override (Safety Feature)
Pilots can take over or land the drone instantly using the following hotkeys:
* `T`: Takeoff & Hover
* `L` / `ESC`: Safe landing and session termination
* `W` / `S`: Move Forward / Backward
* `A` / `D`: Move Left / Right
* `Q` / `E`: Rotate Counter-Clockwise / Clockwise
* `R` / `F`: Rise / Fall

---

## 📦 Prerequisites & Installation

### 1. Dependencies
* Python 3.7+
* OpenCV (`opencv-python`)
* [djitellopy](https://github.com/damiafuentes/DJITelloPy)
* Darknet Framework (YOLOv4 Python wrapper)

### 2. Setup
Clone this repository and install the Python dependencies:
```bash
git clone [https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git](https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git)
cd YOUR_REPO_NAME
pip install djitellopy opencv-python
