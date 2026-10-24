# Cross-Dataset Evaluation of Transfer-Learning CNNs for Musculoskeletal Radiographs

Evaluates three ImageNet-pretrained CNNs (DenseNet121, ResNet50, EfficientNet-B0) trained on a reduced subset of the **MURA** dataset and tested on a held-out MURA split and on the external **FracAtlas** dataset.

## Key results

| Model | MURA test ROC-AUC (95% CI) | FracAtlas ROC-AUC (95% CI) |
|---|---|---|
| DenseNet121 | 0.723 (0.695–0.751) | 0.620 (0.596–0.645) |
| ResNet50 | 0.763 (0.738–0.787) | 0.624 (0.602–0.648) |
| EfficientNet-B0 | 0.752 (0.725–0.778) | 0.583 (0.558–0.607) |

- On MURA, ResNet50 and EfficientNet-B0 both outperform DenseNet121 (paired bootstrap, CIs exclude zero).
- On FracAtlas, discrimination drops for all models, and precision at the MURA-chosen threshold is only 0.25–0.27.
- Models were trained for 6 epochs on 2,000 images with one seed. They are **not converged**.

## Important caveats

- **Data quality.** 41.6% of MURA manifest labels disagree with folder labels, and the test partition's provenance is unverified. See `docs/Verification_Report.md`.
- **Corrected statistics.** An earlier automated summary (`results/RESEARCH_SUMMARY.md`) used an invalid bootstrap. Use `analysis/corrected_bootstrap.json` and `analysis/precision_ci.json` instead.
- **Label mismatch.** MURA's "abnormal" label is not fracture-specific, so the FracAtlas result is an exploratory cross-dataset test.

## Repository structure

```
paper/          Manuscript (DOCX and PDF) and figures used in it
notebooks/      Full analysis notebook (training, evaluation, audit, Grad-CAM)
results/        Metrics, plots, confusion matrices, Grad-CAM, error analysis
analysis/       Recomputed bootstrap and precision confidence intervals
code/           Figure and manuscript build scripts
docs/           Verification report (literature, audit, open items)
```

## Reproducing the results

1. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```
   Install PyTorch first from [pytorch.org](https://pytorch.org) for your CUDA version.

2. **Obtain the data** (not included in this repository; see licences below).
   - MURA v1.1: Stanford ML Group, <https://stanfordmlgroup.github.io/competitions/mura>
   - FracAtlas: Abedeen et al., Scientific Data (2023), doi:10.1038/s41597-023-02432-4 (CC BY 4.0)

   Arrange the prepared data as:
   ```
   prepared_data/
     mura/        train/{normal,abnormal}, val/..., test/...
     fracatlas/   external_test/{normal,abnormal}/...
   ```

3. **Run the notebook** `notebooks/bone_xray_comparison.ipynb` top to bottom. The notebook was written for Google Colab, so `DATA_ROOT` and the Google Drive mount path at the top of the file must be changed for your environment.

4. **Rebuild figures and paper** (optional): `code/make_figs.py`, `code/make_arch.py`, `code/build_paper.py`. These paths refer to the original working directory and need adjusting.

## Notes on the notebook

The notebook was run with `ALLOW_CONTINUE_WITH_BLOCKING_ISSUES = True`, so the audit's blocking issues did not stop the run. The saved per-image predictions in `results/metrics/predictions_*.csv` are the basis for all reported statistics.

## Citation

If you use this work, cite the manuscript (add the final citation once published) and the datasets:

- Rajpurkar et al., *MURA: Large Dataset for Abnormality Detection in Musculoskeletal Radiographs*, arXiv:1712.06957.
- Abedeen et al., *FracAtlas: A Dataset for Fracture Classification, Localization and Segmentation of Musculoskeletal Radiographs*, Scientific Data 10:521 (2023).

