# Jade Color Analysis

Jupyter notebooks for white-reference normalization, CIELAB color analysis, and K-means segmentation of jade sample images.

## Repository contents

```text
.
├── jade-segmentation.ipynb  # Three-cluster segmentation and descriptive statistics
├── k-selection.ipynb       # Elbow analysis for choosing the number of clusters
├── jColor/                # Image normalization and color conversion utilities
├── imgs/                  # Included sample images: M1.jpg through M4.jpg
├── requirements.txt       # Python dependencies
└── LICENSE                # MIT License
```

## Installation

From the repository root, create and activate a Python environment:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
python -m jupyterlab
```

On Windows, activate the environment with `.venv\Scripts\activate` instead.

The dependency file does not pin versions. Record your Python and package versions when reporting results. Background removal uses `rembg`; its first run may download model weights and require internet access.

## Running the notebooks

Open either notebook from the repository root and run its cells in order. Both notebooks default to `SAMPLE = 'M1'`. Change this value to `M2`, `M3`, or `M4` to analyze another included image. Image paths are relative to the repository root.

### Color segmentation

`jade-segmentation.ipynb`:

1. Loads the sample image and estimates a white reference.
2. Removes the background and normalizes RGB values.
3. Converts the normalized image to CIELAB using the D65 reference implemented in `jColor`.
4. Fits K-means to all foreground pixels with three clusters, `n_init=10`, and `random_state=0`.
5. Orders groups A, B, and C by decreasing centroid lightness (L*).
6. Displays the segmentation, cluster centers, group masks, and descriptive statistics for L*, a*, and b*.

The statistics table contains sample, group, channel, mean, standard deviation, minimum, maximum, variance, median, standard error, percentage, and pixel count. Standard deviation and variance use `ddof=0`; standard error is computed as standard deviation divided by the square root of the pixel count. Percentages describe each group's share of foreground pixels and repeat across its three channel rows.

### Cluster-count selection

`k-selection.ipynb` samples at most 100,000 foreground pixels without replacement, using seed 0. It fits K-means for k=1 through 10 with `n_init=10` and `random_state=0`, then plots inertia and its percentage reduction.

The suggested elbow maximizes the distance from the normalized inertia curve to the line joining its endpoints. This notebook does not compute Silhouette scores. Its suggested k does not automatically change the segmentation notebook's three-cluster setting.

## White-reference normalization

The current implementation selects candidate white pixels whose mean RGB intensity is at least 180, then retains candidates whose across-channel standard deviation is at most 5. Their mean RGB values form the white reference. Foreground RGB values are divided by that reference and clipped to [0, 1]. The notebooks use the default normalization and background-removal settings.

## Images

The repository includes `imgs/M1.jpg`, `M2.jpg`, `M3.jpg`, and `M4.jpg`, which are the inputs used by the notebooks. Keep their filenames unchanged unless you also update the corresponding input paths. Additional acquisition and sample-provenance information is not supplied in this README.

## Reproducibility

Notebook outputs are cleared so that plots and tables are generated in the current environment. The English-language update preserves numerical operations, parameters, and control flow; table column names and display labels are in English. Numerical results have not been recomputed as part of this update. Runtime and memory requirements depend on image size and foreground pixel count.

## License

The project code, notebooks, documentation, and included sample images are distributed under the [MIT License](LICENSE). Third-party packages and downloaded model weights retain their own licenses.
