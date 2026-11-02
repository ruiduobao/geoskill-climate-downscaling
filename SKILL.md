---
name: geoskill-climate-downscaling
description: '统计降尺度：用多元回归建立粗分辨率气候变量与高分辨率地形预测因子的关系，并对残差做空间插值，生成高分辨率气候栅格'
---

# 气候降尺度 | Climate Downscaling

This skill implements **statistical downscaling**, which downscales coarse-resolution climate variables (temperature/precipitation) to high resolution. The core workflow is "terrain regression + residual spatial interpolation":

1. Derive predictors from the high-resolution DEM: elevation and slope.
2. Average the DEM and the ground-truth blocks onto the coarse-resolution grid to form regression samples.
3. Build the relationship climate variable ~ elevation + slope with multiple linear regression (scikit-learn `LinearRegression`); in the temperature scenario, the elevation coefficient is the lapse rate.
4. Extrapolate the regression model to the high-resolution grid and interpolate the coarse-grid residuals back to high resolution using scipy linear interpolation.
5. Downscaled result = high-resolution regression prediction + high-resolution interpolated residuals.

Outputs a high-resolution downscaled raster, a regression/residual component stack, and a validation report (correlation coefficient against ground truth, RMSE, improvement over the coarse-resolution baseline, lapse rate). Suitable for regional climate refinement, spatialization of temperature/precipitation over complex terrain, and preparation of forcing fields for ecological and hydrological models.

## Dependencies / 依赖

```bash
pip install 'numpy' 'rasterio' 'scipy' 'scikit-learn'
```

## Usage / 使用方法

### Basic Usage

```bash
python geoskill-climate-downscaling.py --bbox 116.0 39.0 117.0 40.0 [other parameters]
```

### Example 1 (Synthetic Data, Offline)

```bash
python geoskill-climate-downscaling.py --bbox 116.0 39.0 117.0 40.0 --synthetic --output-dir ./out
```

### Example 2 (bbox Only, Automatic Synthesis)

```bash
python geoskill-climate-downscaling.py --bbox 100.0 26.0 104.0 30.0 --output-dir ./out
```

### Example 3 (Specify Target Grid Size)

```bash
python geoskill-climate-downscaling.py --bbox 100.0 26.0 104.0 30.0 --target-resolution 128 --synthetic --output-dir ./out
```

### Example 4 (Quiet Mode)

```bash
python geoskill-climate-downscaling.py --bbox 100.0 26.0 104.0 30.0 --synthetic --output-dir ./out --quiet
```

### Example 5 (Real Raster Input, band1=DEM, band2=Ground Truth, band3=Coarse Climate Field)

```bash
python geoskill-climate-downscaling.py --input dem_truth_coarse.tif --output-dir ./out
```

## Output / 输出

| File | Format | Description |
|---|---|---|
| `downscaled.tif` | GeoTIFF | High-resolution downscaled climate raster |
| `downscaling_components.tif` | GeoTIFF | Regression component + residual component (2 bands) |
| `validation_report.json` | JSON | Regression coefficients, correlation, RMSE, lapse rate |
| `output-manifest.json` | JSON | Run manifest |

## Data Source / 数据源 / Source

- **Real mode**: local multi-band GeoTIFF (band1=high-resolution DEM, band2=ground truth to be downscaled, band3=coarse-resolution climate field).
- **Synthetic mode** (`--synthetic` or `--bbox` only): generates a "temperature decreasing with elevation" scenario locally, with no network access required.

## Privacy / 隐私声明 / Privacy

- Runs offline by default; `--synthetic` mode requires no network access at all.
- All processing is performed locally; no user data is uploaded.

## License / License

MIT

---

> 中文完整版：需要时再读 [references/SKILL.zh-CN.md](references/SKILL.zh-CN.md)，不要默认全量加载。
