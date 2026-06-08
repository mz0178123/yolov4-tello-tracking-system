# Tello Drone Autonomous Tracking with YOLOv4

An autonomous object-tracking and control system for the **DJI Tello** drone using the **YOLOv4 (Darknet)** object detection framework. The system captures live video from the drone's camera, detects target objects in real-time, and automatically adjusts the drone's flight path to keep the target centered in the frame.

---

## 🚀 Features

* **Real-time Object Detection:** Leverages YOLOv4 (Darknet) for low-latency, high-accuracy object detection.
* **Multi-threaded Architecture:** Uses separate Python threads for video capture, AI inference, and drawing/control to ensure a smooth, lag-free video stream.
* **Autonomous Visual Tracking:** Automatically commands the drone to move forward, backward, up, down, or sideways based on the target's bounding box relative to a central safety zone.
* **Manual Keyboard Override:** Allows pilot takeover at any time using standard keyboard hotkeys (`W`, `A`, `S`, `D`, etc.).

---

## 🛠️ System Architecture

The application is split into three main concurrent threads managed by Python's `threading` and `queue.Queue`:

1.  **Video Capture Thread:** Streams live RGB frames from the DJI Tello camera and resizes them to 960x720.
2.  **Inference Thread:** Feeds the processed frames into the YOLOv4 Darknet neural network to extract bounding boxes.
3.  **Drawing & Control Thread:** Renders the detection boxes and a central green safety zone on the UI. It executes drone tracking commands on a ~3-second interval check to prevent command flooding.

---

## ⌨️ Controls

### Manual Flight Controls
* `T`: Takeoff & Hover
* `L` / `ESC`: Land safely and exit
* `W` / `S`: Move Forward / Backward
* `A` / `D`: Move Left / Right
* `Q` / `E`: Rotate Counter-Clockwise / Clockwise
* `R` / `F`: Move Up / Down

### Autonomous Mode
When a target object's center point moves outside the central green boundary box ($420 \le x \le 540$, $175 \le y \le 295$), the drone will automatically adjust its position to re-center the target.

---

## 📦 Prerequisites & Installation

### 1. Requirements
* Python 3.7+
* OpenCV (`opencv-python`)
* [djitellopy](https://github.com/damiafuentes/DJITelloPy)
* Darknet (YOLOv4 Python wrapper compiled with GPU/CUDA support recommended)

### 2. Setup
Clone this repository and install dependencies:
```bash
git clone [https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git](https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git)
cd YOUR_REPO_NAME
pip install djitellopy opencv-python
