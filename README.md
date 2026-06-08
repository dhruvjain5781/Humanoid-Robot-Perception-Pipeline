# Humanoid Robot Perception Pipeline

A full perception stack for humanoid robots — built from a physics simulation all the way to edge deployment benchmarking. Covers synthetic data generation, virtual camera rendering, 3D object localisation, real-time object tracking, point cloud reconstruction, and ONNX inference optimisation.

Runs entirely on **Google Colab T4 GPU**

---

## What this project does


| Stage | Description |
|---|---|
| Physics simulation | PyBullet scene with Kuka iiwa arm and objects — equivalent concepts to NVIDIA IsaacSim |
| Virtual camera | Renders RGB frames and ground-truth depth maps from the simulation |
| 3D projection | Forward and back-projection math — the core of every robotics perception stack |
| Object detection + tracking | YOLOv8n with BoT-SORT multi-object tracking at 50+ FPS |
| Point cloud | Back-projects the depth map into 3D, then voxel-downsamples for efficient scene representation |
| Edge deployment | Exports YOLOv8 to ONNX and benchmarks CPU inference latency vs PyTorch baseline |

---

## Pipeline architecture

```
Physics Sim (PyBullet)
       │
       ├── Virtual Camera → RGB frame + Depth map (ground truth)
       │         │
       │         ├── Camera Intrinsics (fx, fy, cx, cy)
       │         │         └── 2D pixel → 3D world coordinate (X = (u-cx)*Z/fx)
       │         │
       │         └── Point Cloud → Voxel Downsampling → Scene Model
       │
       └── Object Positions (ground truth for validation)

Real Video (warehouse.mp4 — synthetic)
       └── YOLOv8 Tracker → Bounding Boxes + Track IDs + FPS

YOLOv8n Model
       ├── PyTorch inference (GPU baseline)
       └── ONNX export → ONNX Runtime (CPU) → Latency comparison
```

---

## Stack

- **PyBullet** — physics simulation and virtual camera rendering
- **YOLOv8 (Ultralytics)** — real-time object detection and tracking
- **MiDaS** — monocular depth estimation concept (ground-truth depth used here via sim)
- **ONNX / ONNX Runtime** — model export and CPU inference benchmarking
- **OpenCV** — frame processing and visualisation
- **NumPy** — point cloud construction and voxel downsampling (no Open3D dependency)

---

## Results

| Metric | Value |
|---|---|
| Objects localised in 3D | 3 (cube_1, cube_2, sphere) |
| YOLOv8 tracking FPS | 50+ |
| Point cloud reduction (voxel filter) | ~70% |
| ONNX speedup vs PyTorch CPU | ~1.5–2x |
| Estimated TensorRT INT8 on Jetson Orin | ~3–4ms per frame |

---

## How to run

1. Open the notebook in Google Colab
2. Go to **Runtime → Change runtime type → T4 GPU**
3. Run all cells top to bottom

No local GPU required. All dependencies install in cell 1.
