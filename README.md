# Metric-Semantic 3D Scene Reconstruction

### Desktop Socket Pose Estimation via Multi-View YOLO Detection and Ray Triangulation

**Course:** CP260-2026
**Authors:**

* **Kasa Pavan** (Roll No: 26738)
  Email: [kasapavan@iisc.ac.in](mailto:kasapavan@iisc.ac.in)
* **Vidhyasagar Murugan**

---

## 📌 Overview

This project focuses on **metric-semantic 3D scene reconstruction** using multi-view RGB images with known camera poses.

The goal is to localize desktop components:

* Power Socket
* Ethernet Socket
* VGA Socket (Bonus)

We estimate full **6-DoF pose** using:

* 2D object detection
* Multi-view triangulation
* Oriented Bounding Box (OBB) reconstruction

---

## ⚙️ Pipeline

The system follows a 5-stage pipeline:

1. **Dataset Preparation**
2. **YOLOv9m Training**
3. **Multi-frame Detection**
4. **Multi-view Ray Triangulation**
5. **OBB Estimation**

```
Images + Poses
      ↓
YOLO Detection (2D)
      ↓
Ray Construction
      ↓
Multi-view Triangulation
      ↓
3D Center Estimation
      ↓
OBB (Center + Rotation + Extent)
```

---

## 🧠 Methodology

### 1. Object Detection

* Model: **YOLOv9m**
* Trained on 16 annotated images
* Heavy augmentation:

  * Mosaic
  * MixUp
  * Copy-Paste

---

### 2. Multi-View Triangulation

Each detection is converted into a 3D ray:

```
d_cam = [(u - cx)/fx, (v - cy)/fy, 1]
```

Rays are transformed to world coordinates and intersected using least squares:

```
(Σ Mi) p = Σ Mi oi
```

---

### 3. OBB Estimation

* **Center:** from triangulation
* **Rotation:** from viewing direction averaging
* **Extent:** from projected bounding box

---

## 📊 Results

| Metric    | Value |
| --------- | ----- |
| Precision | 0.335 |
| Recall    | 0.583 |
| mAP@50    | 0.316 |
| mAP@50:95 | 0.181 |

* Achieved **< 8 cm center error**
* Achieved **< 10° rotation error**

---

## 📁 Repository Structure

```
├── src/                # Core implementation
├── data/               # Dataset (optional)
├── docs/               # Report PDF
├── README.md
```

---

## 🚀 How to Run

### 1. Install Dependencies

```bash
pip install ultralytics opencv-python numpy
```

---

### 2. Train YOLO

```python
from ultralytics import YOLO

model = YOLO("yolov9m.pt")

model.train(
    data="data.yaml",
    epochs=200,
    imgsz=640,
    batch=8
)
```

---

### 3. Run Detection

```python
results = model.predict(source="frames/")
```

---

### 4. Run Triangulation

Use provided scripts in `/src` to:

* Convert detections → rays
* Perform least squares triangulation
* Generate OBB

---

## 🔬 Future Work

An extension of this work is to incorporate an **embedding-based verification module**:

* Extract feature vectors from YOLO backbone
* Compute class-wise prototype embeddings
* Use cosine similarity for filtering detections

This can improve robustness and reduce false positives.

---

## ⚠️ Limitations

* Small dataset (16 images)
* Performance drops at extreme viewing angles
* Assumes objects visible across multiple views

---

## ⭐ Bonus Work

* VGA socket generalization
* Confidence-based filtering
* Physical prior constraints

---

## 📚 References

1. YOLO: Real-Time Object Detection (CVPR 2016)
2. Ultralytics YOLOv9
3. Multiple View Geometry – Hartley & Zisserman

---

## 🙌 Acknowledgements

This project was completed as part of the **CP260-2026 course** on Metric-Semantic Reconstruction.

---
