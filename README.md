# Cell Segmentation Models

Trained Cellpose model checkpoints and the pipeline used to run them, for the Vasquez Lab cell segmentation workflow.

## Repo structure

```
cell_segmentation_models/
├── models/                              # Trained Cellpose checkpoints (Git LFS)
│   ├── cell_seg_v2_fullimg_epoch_0170
│   └── cell_seg_v3_masksonly_epoch_0190
└── scripts/                             # Pipeline notebook + environment
    ├── cell_seg_pipeline_tiff.ipynb
    ├── environment.yml
    └── README.md                        # Full setup instructions
```

## What the pipeline does

`scripts/cell_seg_pipeline_tiff.ipynb` runs a 3-step pipeline on raw TIFF stacks:

1. **3D CLAHE** — contrast-enhances raw TIFF stacks
2. **Bulk segmentation** — runs a trained Cellpose model on the CLAHE stacks
3. **Post-processing** — measures cell volumes across the dataset, plots the distribution, and flags small/large outliers via multi-Otsu thresholding

## Getting started

See **[`scripts/README.md`](scripts/README.md)** for full setup instructions, including:
- Creating the conda environment (pinned to Cellpose 4.1.1 / Cellpose-SAM, matching the cluster)
- GPU setup (cluster vs. Mac)
- Registering the Jupyter kernel
- What to do if you already have Cellpose installed
- Updating the notebook's data paths before running

## Heads up before cloning

The model checkpoints in `models/` are tracked via **Git LFS** and are ~1.16GB each (~2.3GB total). Cloning this repo will download all of that. We're currently on GitHub's free LFS tier (1GB storage/bandwidth), so please be mindful of repeated large clones/pulls if usage becomes an issue for the lab.

## Questions

Ping Isabel (`isabelmn10`) with any setup issues.
