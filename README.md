Zero-DCE: Low-Light Image and Live Video Enhancement
A Digital Image Processing project implementing Zero-Reference Deep Curve Estimation (Zero-DCE) for low-light image enhancement, with an extension for live webcam/video enhancement.

The project explores conventional enhancement methods—Histogram Equalization (HE), Contrast Limited Adaptive Histogram Equalization (CLAHE), and Gamma Correction—and compares their fixed transformations with the image-adaptive curve estimation performed by DCE-Net.
Overview
Images captured under poor illumination often suffer from low visibility, reduced contrast, and loss of detail. This project uses a lightweight convolutional neural network, DCE-Net, to estimate pixel-wise enhancement curves. The model is trained using zero-reference loss functions rather than paired low-light and normal-light images as direct reconstruction targets.

The trained image model is also reused for webcam enhancement. Each captured frame is processed independently using the existing DCE-Net architecture, checkpoint, and curve application function.
Features
- Low-light image enhancement using Zero-DCE / DCE-Net.
- Iterative pixel-wise curve adjustment.
- Zero-reference training with spatial, exposure, colour, and illumination-smoothness losses.
- Classical enhancement baselines: HE, CLAHE, and Gamma Correction.
- Quantitative and qualitative image evaluation.
- Live webcam enhancement using a trained checkpoint.
- Side-by-side display of original and enhanced frames.
- FPS measurement and optional enhanced-video recording, where enabled in the implementation.
Methodology
The enhancement pipeline consists of:
1. Normalize the low-light RGB image to the range [0, 1].
2. Pass the image through DCE-Net to estimate pixel-wise curve parameters.
3. Apply the estimated curves iteratively.
4. Produce the enhanced image.
The basic curve transformation is
\[
\mathcal{C}(I;\alpha) = I + \alpha I(1-I),
\]
where \(I\) is the normalized input intensity and \(\alpha\) is the estimated curve parameter.
DCE-Net Architecture
The implementation uses a DCE-Net with:
- Seven convolutional layers.
- 3 × 3 convolution kernels.
- 32 intermediate feature channels.
- ReLU activations in intermediate layers.
- Tanh output activation for curve parameters.
- Feature concatenation / skip connections.
- Eight enhancement iterations.
For an RGB image and eight iterations, the network predicts 24 curve parameter maps:
\[
8 \times 3 = 24.
\]
These parameter maps are passed to the curve application function to progressively enhance the image.
Loss Functions
The network is trained without a paired pixel-wise reconstruction target. The training objective combines zero-reference losses:
Loss	Purpose
Spatial consistency	Encourages preservation of local spatial relationships and contrast.
Exposure control	Guides the enhanced image towards a target exposure level.
Colour constancy	Encourages consistency among colour channels and reduces unwanted colour casts.
Illumination smoothness / TV	Encourages neighbouring curve parameters to vary smoothly.


The overall objective is a weighted combination of these losses. Weights and the exposure target are configurable and may differ between experiment runs.
Classical Enhancement Baselines
Method	Principle	Main limitation
Histogram Equalization (HE)	Redistributes the global intensity histogram.	Does not adapt to local illumination and may amplify noise.
CLAHE	Performs histogram equalization over local tiles with contrast clipping.	Results depend on tile size and clip limit; noise may still be amplified.
Gamma Correction	Applies a fixed power-law intensity transformation.	A single gamma value may not suit spatially varying illumination.
DCE-Net	Learns image-dependent pixel-wise enhancement curves.	Requires training and more inference computation.


For colour images, classical methods should be applied consistently—for example, to a luminance channel—to reduce colour shifts caused by independently processing RGB channels.
Dataset
The image enhancement experiments use the LOL (Low-Light) dataset.

The project setup used the following directories:
LOL/
├── our485/   # Training images
└── eval15/   # Evaluation images
Update dataset paths in the configuration or notebook to match your local setup. The training approach is zero-reference: normal-light images are not used as paired reconstruction targets during optimization. Corresponding references may be used for evaluation where available.

Follow the dataset provider's terms and citation requirements when downloading and using LOL.
Live Video Enhancement
The trained image enhancement model is reused for webcam input; the network does not need to be retrained specifically for video.
Processing pipeline
Webcam frame (BGR)
        |
        v
Convert BGR to RGB
        |
        v
Resize / normalize frame
        |
        v
Convert frame to PyTorch tensor
        |
        v
             DCE-Net
        |
        v
Predicted curve parameter maps
        |
        v
Apply enhancement curves
        |
        v
Enhanced RGB frame
        |
        v
Convert to display format
        |
        v
Display original and enhanced frames
Implementation details
- Frames are captured using OpenCV.
- BGR frames are converted to RGB before inference.
- Frames are normalized and converted to PyTorch tensors.
- DCE-Net predicts curve parameter maps for each frame.
- The existing curve application function produces the enhanced frame.
- Original and enhanced frames are displayed side by side.
- Processing throughput (FPS) can be measured.
- An optional video writer can save enhanced output if enabled.
The webcam implementation used a target resolution of 640 × 480. Actual FPS depends on the hardware, model, resolution, and inference implementation.
Checkpoint used for the video extension
s5_seed1_w_exp0.75_exp_target0.4_w_tv5000.0_w_spa2.0
Place the checkpoint at the location expected by the inference script, or update the checkpoint path in that script.
Temporal consistency
Frames are enhanced independently. The current pipeline does not explicitly enforce temporal consistency between consecutive frames. Changes in predicted enhancement parameters may therefore cause visible flicker. Temporal stabilization or temporal consistency losses are possible future improvements.
Experimental Configuration
One set of experiments used the following configuration values. Values may differ between runs.
Parameter	Value
Enhancement iterations	8
Epochs	30
Spatial loss weight	2.0
Exposure loss weight	0.5 or 0.75, depending on the run
Colour loss weight	0.5
Exposure target	0.40 or 0.50, depending on the run
TV loss weight	Run-dependent; 5000.0 was used in the selected video checkpoint


Refer to the configuration associated with each experiment or checkpoint when reproducing a run.
Installation
Requirements
- Python (use a version supported by your installed PyTorch build)
- PyTorch
- NumPy
- OpenCV
- Pillow
- scikit-image
- Matplotlib (for plotting/visualization, if used by the notebooks)
Create and activate a virtual environment:
python -m venv .venv
Windows:
.venv\Scripts\activate
Linux / macOS:
source .venv/bin/activate
Install the common dependencies:
pip install numpy opencv-python pillow scikit-image matplotlib
Install PyTorch using the command recommended for your operating system and CPU/GPU configuration:

https://pytorch.org/get-started/locally/

The project can run on CPU, although training and live inference may be slower than on a supported GPU.
Running the Project
The exact script and notebook filenames depend on the final repository layout. Update this section with the entry points committed to your repository.
Image enhancement
Run the image inference notebook or script, load a trained checkpoint, and pass a low-light image through DCE-Net and the curve application function.
Training
Set the LOL dataset paths and experiment configuration in the training notebook or script, then execute the training workflow. Save checkpoints together with their configurations to support reproducibility.
Live webcam enhancement
Run the webcam inference entry point after configuring:
- The trained checkpoint path.
- Webcam device index (commonly 0 for the default camera).
- Capture resolution.
- Inference device (cpu or a supported cuda device).
Use the actual script filename and command-line arguments from your implementation when adding the final run command here.
Evaluation
The project uses quantitative and qualitative evaluation to examine enhancement quality.
Metric	Description
PSNR	Pixel-level similarity to a normal-light reference image.
SSIM	Structural similarity to a reference image.
Mean brightness	Average image intensity.
Contrast	Variation in image intensity.
Processing time / FPS	Computational performance, particularly for video inference.


For a fair comparison, evaluate HE, CLAHE, Gamma Correction, and DCE-Net on the same evaluation images, using the same references and metric implementations. Report only measured values from completed experiments. In particular, measure FPS on the target hardware rather than estimating it from the webcam resolution.
Limitations and Future Work
- Noise: Enhancement may make sensor noise more visible in dark regions.
- Temporal flicker: Independent frame-wise enhancement does not explicitly enforce consistency across video frames.
- Inference speed: Processing speed depends on hardware and model execution time.
- Parameter sensitivity: Results depend on learned parameters and training configuration.
- Generalization: Performance may vary for illumination conditions that differ from those represented in the training data.
Possible future work includes temporal consistency mechanisms for video, inference optimization, evaluation on additional low-light datasets, and a systematic quantitative comparison with classical enhancement methods.

Acknowledgements
This project is based on the Zero-Reference Deep Curve Estimation for Low-Light Image Enhancement (Zero-DCE) approach.
