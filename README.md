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

## Acknowledgements

Based on **Zero-Reference Deep Curve Estimation for Low-Light Image Enhancement (Zero-DCE)**.
