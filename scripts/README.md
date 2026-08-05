# Cell Segmentation Pipeline — Setup & Usage

This folder contains the segmentation pipeline notebook (`cell_seg_pipeline_tiff.ipynb`) and the conda environment needed to run it (`environment.yml`).

## 1. Create the environment

```bash
conda env create -f environment.yml
conda activate cell_analysis
```

## 2. GPU setup (do this after creating the environment)

`pip install cellpose` installs a **CPU-only** version of PyTorch by default. The notebook calls `models.CellposeModel(gpu=True, ...)`, so you'll want GPU acceleration if it's available on your machine.

**On the lab cluster (Tillicum/Hyak, NVIDIA GPUs):**
```bash
pip install torch --index-url https://download.pytorch.org/whl/cu121
```
(Check your node's CUDA version with `nvidia-smi` and adjust `cu121` if needed — see [pytorch.org/get-started/locally](https://pytorch.org/get-started/locally/) for the right index URL.)

**On a Mac (Apple Silicon, M1/M2/M3/M4):**
The default `pip install torch` already includes MPS (Apple GPU) support — no extra step needed. Cellpose will use `gpu=True` via MPS automatically.

**No GPU available:**
You can leave `gpu=True` in the notebook — Cellpose will fall back to CPU automatically, just slower.

## 3. Register the Jupyter kernel

So `cell_analysis` shows up as a selectable kernel in VS Code or Jupyter Lab:

```bash
python -m ipykernel install --user --name cell_analysis --display-name "cell_analysis"
```

Then in VS Code / Jupyter, select **cell_analysis** as the kernel when opening the notebook.

## 4. Update paths before running

Open `cell_seg_pipeline_tiff.ipynb` and edit the **Config** cell — these paths are specific to whoever ran it last and need to point to your own data:

| Variable | What it is |
|---|---|
| `raw_dir` | Folder of raw input TIFF stacks |
| `clahe_dir` | Output folder for CLAHE-enhanced stacks (created automatically) |
| `mask_dir` | Output folder for segmentation masks (created automatically) |
| `results_dir` | Output folder for CSVs and plots (created automatically) |
| `model_path` | Path to the trained Cellpose model checkpoint (e.g. `../models/cell_seg_v2_fullimg_epoch_0170`) |

## 5. Run the notebook

Run cells top to bottom. The pipeline has three stages:
1. **3D CLAHE** — contrast-enhances raw TIFF stacks
2. **Bulk segmentation** — runs the trained Cellpose model on the CLAHE stacks
3. **Post-processing** — measures cell volumes, plots the distribution, and flags small/large outliers

##Parameters
Here are some of the parameters you can change:

1. flow_threshold is the maximum allowed error of the flows for each mask. The default is 0.4.
  Increase this threshold if cellpose is not returning as many masks as you’d expect (or turn off completely with 0.0)
  Decrease this threshold if cellpose is returning too many ill-shaped masks.
2. cellprob_threshold determines probability that a detected object is a cell. The default is 0.0.
  Decrease this threshold if cellpose is not returning as many masks as you’d expect or if masks are too small
  Increase this threshold if cellpose is returning too many masks esp from dull/dim areas.

## Notes

- **Cellpose version:** pinned to `4.1.1` (Cellpose-SAM) to match what's confirmed running on the lab cluster.
- **`channels` parameter:** Cellpose-SAM disregards the `channels=[0, 0]` argument used in `model.eval()` — this is expected behavior in v4.0.1+, not a bug.
- Model checkpoints live in `../models/` in this repo (tracked via Git LFS).
