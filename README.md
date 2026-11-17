# Zero-DCE: Low-Light Image and Video Enhancement

A Digital Image Processing project using **Zero-Reference Deep Curve Estimation (Zero-DCE)** to enhance low-light images and live webcam video.

## Features

- Low-light enhancement using DCE-Net.
- Classical methods: Histogram Equalization (HE), CLAHE, and Gamma Correction.
- Zero-reference training with spatial, exposure, colour, and illumination-smoothness losses.
- Live webcam enhancement using a trained checkpoint.
- Side-by-side display of original and enhanced frames.

## Method

DCE-Net predicts pixel-wise curve parameters to enhance image illumination iteratively.

\[
\mathcal{C}(I;\alpha)=I+\alpha I(1-I)
\]

The implementation uses seven convolutional layers, eight enhancement iterations, and 24 output parameter maps for RGB images.

## Dataset

Experiments use the **LOL low-light dataset**:

```text
LOL/
├── our485/   # Training images
└── eval15/   # Evaluation images
```

## Live Video Enhancement

The trained image model is reused for webcam input:

1. Capture a frame using OpenCV.
2. Convert BGR to RGB and normalize.
3. Run DCE-Net to predict curve parameters.
4. Apply the enhancement curves.
5. Display original and enhanced frames.

The webcam pipeline uses a target resolution of **640 × 480**. Frames are processed independently, so temporal flicker may occur.

Checkpoint used:

```text
s5_seed1_w_exp0.75_exp_target0.4_w_tv5000.0_w_spa2.0
```

## Installation

```bash
pip install numpy opencv-python pillow scikit-image matplotlib
```

Install PyTorch for your CPU/GPU setup: https://pytorch.org/get-started/locally/

## Evaluation

Methods can be compared using:

- PSNR and SSIM
- Mean brightness and contrast
- Processing time / FPS

Evaluate all methods on the same images for a fair comparison.

## Repository Structure

Adapt this structure to your implementation:

```text
Zero-DCE/
├── notebooks/
├── src/
├── checkpoints/
├── results/
├── requirements.txt
└── README.md
```

## Acknowledgements

Based on **Zero-Reference Deep Curve Estimation for Low-Light Image Enhancement (Zero-DCE)**. Please cite the original paper and LOL dataset.
