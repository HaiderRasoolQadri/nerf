# NeRF Reproduction

## Overview
This fork reproduces the results of:
> Mildenhall et al., "NeRF: Representing Scenes as 
> Neural Radiance Fields for View Synthesis", ECCV 2020

## Environment
| Component | Version |
|-----------|---------|
| GPU | NVIDIA Tesla T4 (15GB) |
| Platform | Google Colab Free Tier |
| TensorFlow | 2.19.0 |
| Python | 3.12 |
| imageio | 2.37.3 |

## TF2 Compatibility Fixes Applied
The original code was written for TensorFlow 1.15.
Six fixes were required to run under TensorFlow 2.19:

| # | File | Issue | Fix |
|---|------|-------|-----|
| 1 | run_nerf_helpers.py | tf.log() removed in TF2 | tf.math.log() |
| 2 | load_blender.py | tf.image.resize_area() removed | tf.image.resize(..., AREA) |
| 3 | run_nerf_helpers.py | keras.Input shape not tuple | Added trailing comma |
| 4 | Script level | Keras 3 KerasTensor incompatible | TF_USE_LEGACY_KERAS=1 |
| 5 | run_nerf.py | check_numerics NaN abort | Replaced with pass |
| 6 | load_llff.py | ignoregamma removed in imageio v2 | Removed argument |

## How To Reproduce

### Step 1: Install dependencies
```bash
pip install configargparse imageio-ffmpeg lpips tf_keras
```

### Step 2: Download weights and data
```bash
bash download_example_weights.sh
bash download_example_data.sh
```

### Step 3: Run inference on Lego scene
```bash
TF_USE_LEGACY_KERAS=1 python run_nerf.py \
    --config config_lego.txt \
    --expname lego_example \
    --render_only \
    --render_test
```

### Step 4: Run inference on Fern scene
```bash
TF_USE_LEGACY_KERAS=1 python run_nerf.py \
    --config config_fern.txt \
    --expname fern_example \
    --render_only \
    --render_test
```

## Results

### Lego Scene (Synthetic)
| Metric | Paper | Ours |
|--------|-------|------|
| PSNR | 32.54 | 32.11 |
| SSIM | 0.961 | 0.9669 |
| LPIPS | 0.050 | 0.0186 |

### Fern Scene (Real)
| Metric | Paper | Ours |
|--------|-------|------|
| PSNR | 25.17 | 24.40 |
| SSIM | 0.792 | 0.8076 |
| LPIPS | 0.280 | 0.0987 |

## Experiment Logs
See `experiment_logs/` folder for per-frame metrics.
