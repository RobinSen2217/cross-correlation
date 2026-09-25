# Cross-Correlation User Interface

An FFT-based, GPU-accelerated tool for windowed cross-correlation analysis between two images. Given a pair of image frames (e.g. sequential observations of a drifting or evolving structure), it computes a spatial map of local correlation strength using an interactive, GPU-accelerated backend — designed to run entirely inside Google Colab.

This repository accompanies an in-progress research paper and is intended as the reference implementation of the method described there.

## Overview

The tool splits each input image into overlapping windows, computes the normalized cross-correlation between corresponding windows in Frame 1 and Frame 2 via FFT, and reconstructs a correlation-strength map at the original image resolution. This is useful for tracking coherent structures, motion, or displacement patterns between two images of the same scene taken at different times.

**Core steps:**
1. Convert both input frames to grayscale.
2. Extract overlapping windows of size `wsize × wsize` with a configurable stride (`wsize - overlap`).
3. Zero-mean each window and compute cross-correlation via FFT (`torch.fft.rfft2` / `irfft2`), normalized by window norms.
4. Take the peak correlation value per window pair to build a low-resolution correlation grid.
5. Upsample (nearest-neighbor) the correlation grid back to the original image resolution for visualization.

## Requirements

- **Google Colab** (GPU runtime recommended, but falls back to CPU automatically)
- Python packages (pre-installed in Colab): `numpy`, `torch`, `Pillow`, `ipython`

> **Note:** This notebook is built specifically for the Google Colab environment. It relies on `google.colab.output.register_callback` and `google.colab.kernel.invokeFunction` to bridge the browser-based UI with the Python kernel, and will not run as-is in Jupyter/JupyterLab or VS Code notebooks.

## Usage

1. Open `CC_GUI.ipynb` in Google Colab.
2. (Optional but recommended) Set the runtime to use a GPU: **Runtime → Change runtime type → GPU**.
3. Run all cells. A "Workspace Ready" button will appear, and an interactive workspace window will open automatically (allow pop-ups if blocked).
4. In the workspace:
   - Upload **Frame 1** and **Frame 2** (the two images to compare).
   - Adjust the **Window Size** and **Overlap** sliders as needed.
   - Click **Execute** to compute the cross-correlation map.
5. The resulting correlation map is displayed inline in the workspace.

Example input frames are provided in the [`Example Frames`](./Example%20Frames) folder for testing.

## Parameters

| Parameter | Description | Default |
|---|---|---|
| `wsize` (Window Size) | Side length (in pixels) of each square correlation window | 16 |
| `overlap` | Pixel overlap between adjacent windows (stride = `wsize - overlap`) | 8 |

Smaller windows give finer spatial resolution but noisier correlation estimates; larger windows are more robust but coarser.

## Output

The result is a grayscale correlation map, upsampled to the input image resolution, where pixel intensity encodes local correlation strength between the two frames (darker regions indicate higher correlation, per the current normalization).

## Repository Structure

```
cross-correlation/
├── CC_GUI.ipynb       # Main notebook: algorithm + interactive Colab UI
└── Example Frames/    # Sample image pairs for testing
```

## Citation

If you use this tool in your research, please cite the associated paper (citation details to be added upon publication).
