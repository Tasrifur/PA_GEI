# PA-GEI: Part-Aware Gait Energy Images

PyTorch code for PA-GEI, a silhouette-based gait recognition method. The Gait Energy Image (GEI) is split into N horizontal strips before any convolution, and each strip is encoded by its own lightweight CNN. The strip descriptors are concatenated and matched by cosine similarity.

## Contents

| File | Description |
| --- | --- |
| `PA_GEI_CASIA-B.ipynb` | Full pipeline: GEI construction, models, training, evaluation, ablation |
| `requirements.txt` | Python dependencies |
| `LICENSE` | MIT licence |

## Requirements

Python 3 with a CUDA GPU. The experiments were run on Google Colab with an NVIDIA T4.

```bash
pip install -r requirements.txt
```

## Data

This repository does not include any CASIA-B data. Request the CASIA Gait Database (Dataset B) from the Institute of Automation, Chinese Academy of Sciences:
http://www.cbsr.ia.ac.cn/english/Gait%20Databases.asp

The notebook expects size-normalised, horizontally centred 64×64 silhouettes, zipped with the subject folders at the archive root:

```
casia-b-silhouettes.zip
└── 001/ … 124/
    └── nm-01/ … cl-02/
        └── 000/ … 180/
            └── *.png
```

## Running

1. Open `PA_GEI_CASIA-B.ipynb` in Google Colab and select a GPU runtime.
2. Upload the zip to Google Drive and set `zip_path` in the data cell. Checkpoints, figures and CSV files are written to `/content/drive/MyDrive/CASIA_B/`; change these paths if needed.
3. Run all cells in order.

Outside Colab, remove the `google.colab` cell and point `zip_path`, `extract_path` and the save paths to local folders.

## Configurations

- **PA-GEI (proposed):** `PartGEINet(..., use_part_attention=False)`, trained in the "Part Attention ON vs OFF" cell.
- **Fixed per-strip weight:** `use_part_attention=True`. Cells labelled "Proposed" in the notebook use this variant; it is reported as an ablation.
- **Baseline:** `FullGEINet`, a ResNet-18 on the full GEI.
- **Part count:** `N_PARTS = 4` by default; N = 2 and 6 are run in the ablation cell.

## Protocol

- Training subjects 001–074; test subjects 075–124.
- Gallery: NM 01–04. Probes: NM 05–06, BG 01–02, CL 01–02.
- Each probe is matched against a pooled gallery of all views except its own (`exclude_same_angle=True`). Rank-1 accuracy is reported per condition with the unweighted mean.
- The per-angle cells near the end include same-view gallery entries and use the fixed-weight model; they are exploratory.

## Reproducibility

No random seed is fixed, so reruns give slightly different numbers. The saved cell outputs are from the reported run. Two changes were made to that record: the explicit `N_PARTS` argument was added to `split_gei_parts` in the two visualisation cells so the notebook runs top to bottom, and the image outputs of those two cells were removed so that no CASIA-B imagery is redistributed.

## Acknowledgment

Portions of the research in this repository use the CASIA Gait Database collected by Institute of Automation, Chinese Academy of Sciences.

## License

MIT. See `LICENSE`.
