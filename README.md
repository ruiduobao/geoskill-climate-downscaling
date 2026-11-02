# Climate Downscaling (geoskill-climate-downscaling)

> Statistical downscaling: relates coarse-resolution climate variables to high-resolution terrain predictors via multiple regression and spatially interpolates the residuals to produce high-resolution climate rasters

---

## 1. Overview

This skill implements **statistical downscaling** to downscale coarse-resolution climate variables (temperature/precipitation) to high resolution, with the core workflow of "terrain regression + residual spatial interpolation": 1. Extract predictors from a high-resolution DEM: elevation and slope. 2. Block-average the DEM and the ground truth to coarse-resolution grid points to form regression samples. 3. Build the relationship climate variable ~ elevation + slope with multiple linear regression (scikit-learn `LinearRegression`); for temperature, the elevation coefficient is the lapse rate. 4. Extrapolate the regression model to the high-resolution grid and interpolate the coarse-grid residuals back to high resolution using scipy linear interpolation. 5. Downscaled result = high-resolution regression prediction + high-resolution interpolated residuals. Outputs a high-resolution downscaled raster, a regression/residual component stack, and a validation report (correlation with ground truth, RMSE, improvement over the coarse-resolution baseline, lapse rate). Suitable for regional climate refinement, spatialization of temperature/precipitation over complex terrain, and preparation of forcing fields for ecological and hydrological models.

## 3. Quick Start

```bash
pip install -r requirements.txt
python geoskill-climate-downscaling.py --bbox 116 39 117 40 --synthetic --output-dir ./out
```

## 4. CLI Parameters

Run `python <skill>.py --help` for the full list. Common parameters:

| Parameter | Type | Description |
|---|---|---|
| `--bbox` | `float[4]` | WGS84 bounding box `min_lon min_lat max_lon max_lat` |
| `--input` | `path` | Local input file (GeoJSON/GeoTIFF/etc.) |
| `--output-dir` | `path` | Output directory (default `./output`) |
| `--synthetic` | `flag` | Use synthetic data instead of real input |
| `--quiet` | `flag` | Suppress non-essential stdout |

## 5. Input / Output

| File | Format | Description |
|---|---|---|
| `downscaled.tif` | GeoTIFF | High-resolution downscaled climate raster |
| `downscaling_components.tif` | GeoTIFF | Regression component + residual component (2 bands) |
| `validation_report.json` | JSON | Regression coefficients, correlation, RMSE, lapse rate |
| `output-manifest.json` | JSON | Run manifest |


## 6. Technical Principle

(see SKILL.md for details)

## 8. License

MIT License. See [`LICENSE`](./LICENSE) for full text.

---

> 中文完整版：[references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)
