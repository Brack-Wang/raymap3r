<div align="center">

# RayMap3R: Inference-Time RayMap for Dynamic 3D Reconstruction

This is the official implementation of **RayMap3R**.

[![Project Page](https://img.shields.io/badge/Project-Page-green)](https://raymap3r.github.io)
[![arXiv](https://img.shields.io/badge/arXiv-Paper-b31b1b)](https://arxiv.org/)

</div>

<div align="center">
<img src="asset/teaser.png" width="90%">
</div>

<p align="center"><b>Streaming 3D Reconstruction for Dynamic Scenes.</b> Existing streaming methods such as CUT3R and TTT3R can suffer from camera drift caused by moving objects. RayMap3R identifies and suppresses dynamic regions at inference time without additional training or external models.</p>

---

## Overview

Streaming feed-forward 3D reconstruction enables real-time joint estimation of scene geometry and camera poses from RGB images. However, without explicit dynamic reasoning, streaming models can be affected by moving objects, causing artifacts and drift.

**RayMap3R** is a training-free streaming framework that addresses this by exploiting a key observation: **RayMap predictions exhibit a static-scene bias**. When only camera rays are provided without the actual image, the model reconstructs only the static background and ignores dynamic objects. We leverage this bias to identify and suppress dynamic regions at inference time.

### Key Features

- **Static-Scene Bias Discovery** &mdash; RayMap-only predictions inherently ignore dynamic objects, providing a built-in signal for dynamic identification without external models
- **Dual-Branch Inference** &mdash; Contrasts image-based and RayMap-only predictions to derive per-pixel staticness weights that gate memory updates
- **Reset Metric Alignment** &mdash; Aligns point clouds before and after memory resets via Sim(3) estimation for globally consistent geometry
- **State-Aware Smoothing** &mdash; Adaptively smooths trajectories using acceleration and state change magnitude as an uncertainty signal
- **Real-time & Constant Memory** &mdash; Processes video streams with constant memory usage and real-time efficiency

If you find this repository useful, please give it a star🌟 and consider citing our paper!


---

## Static-Scene Bias

<div align="center">
<img src="asset/raymap_compare.png" width="85%">
</div>

<p align="center">The RayMap branch reconstructs primarily static structure, while the main branch captures the full scene including dynamic objects. Their per-pixel depth discrepancy aligns well with the ground-truth dynamic mask.</p>

<div align="center">
<img src="asset/illustrate.png" width="85%">
</div>

<p align="center"><b>Left:</b> Dual-branch contrast reveals dynamic regions. <b>Right:</b> Dynamic mask IoU vs. ground-truth dynamic ratio across 108 sequences (Spearman ρ = 0.77).</p>

---

## Method

<div align="center">
<img src="asset/pipeline.png" width="90%">
</div>

<p align="center"><b>Pipeline Overview.</b> At each timestep, the main branch predicts depth and pose from image + RayMap features, while the RayMap branch queries the same frozen state using only camera-ray tokens. The depth discrepancy is projected onto state tokens via cross-attention to form staticness weights, which gate memory updates.</p>

---

## Results

### Qualitative Comparison

<div align="center">
<img src="asset/qualitative.png" width="90%">
</div>

<p align="center">Comparison with CUT3R and TTT3R on dynamic DAVIS sequences. RayMap3R produces more coherent point clouds with fewer ghosting artifacts and reduced camera drift.</p>

### Camera Pose Estimation

<div align="center">
<img src="asset/camera_pose.jpg" width="80%">
</div>

<p align="center">Among streaming (online) methods, RayMap3R achieves the lowest ATE on all three pose benchmarks and the lowest Abs Rel on KITTI and Bonn.</p>

---

## Citation

If you find this work useful, please consider citing:

```bibtex
@article{wang2026raymap3r,
  title   = {RayMap3R: Inference-Time RayMap for Dynamic 3D Reconstruction},
  author  = {Wang, Feiran and Shang, Zezhou and Liu, Gaowen and Yan, Yan},
  year    = {2026}
}
```

---

## Acknowledgements

We thank the authors of [CUT3R](https://github.com/CUT3R/CUT3R) and [TTT3R](https://github.com/rover-xingyu/TTT3R) for their excellent work.

## License

This project is released under the [MIT License](LICENSE).
