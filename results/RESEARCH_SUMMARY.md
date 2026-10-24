# Research results summary
**Study:** DenseNet121 vs ResNet50 vs EfficientNet-B0 (ImageNet transfer learning) on MURA, with FracAtlas as exploratory external evaluation.
**Positive class:** abnormal (MURA) / fractured (FracAtlas). **Threshold:** chosen on MURA validation (val_youden), then frozen. **Checkpoint selection:** val_auc. **Imbalance:** inverse-frequency pos_weight = 1.000 from training labels. Bootstrap CIs: 100 resamples.

## Main comparison
| Model | MURA accuracy | MURA precision | MURA recall | MURA specificity | MURA F1 | MURA ROC-AUC | FracAtlas ROC-AUC | FracAtlas F1 | Params | Training time (min) | Latency bs=1 (ms) | Latency bs=batch (ms/img) |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| DenseNet121 | 0.663 | 0.644 | 0.702 | 0.626 | 0.672 | 0.723 | 0.620 | 0.345 | 7216513 | 3.463 | 16.775 | 3.013 |
| ResNet50 | 0.699 | 0.720 | 0.635 | 0.762 | 0.675 | 0.763 | 0.624 | 0.332 | 24032833 | 3.360 | 5.681 | 2.957 |
| EfficientNet-B0 | 0.694 | 0.719 | 0.618 | 0.767 | 0.665 | 0.752 | 0.583 | 0.282 | 4335741 | 3.327 | 7.934 | 1.358 |

95% CIs:

| Model | MURA accuracy | MURA precision | MURA recall | MURA specificity | MURA f1 | MURA roc_auc | FracAtlas roc_auc | FracAtlas f1 | FracAtlas recall | FracAtlas specificity |
|---|---|---|---|---|---|---|---|---|---|---|
| DenseNet121 | 0.663 [0.654, 0.784] | 0.644 [0.635, 0.939] | 0.702 [0.693, 0.815] | 0.626 [0.455, 0.635] | 0.672 [0.663, 0.872] | 0.723 [0.658, 0.726] | 0.620 [0.592, 0.644] | 0.345 [0.319, 0.372] | 0.540 [0.503, 0.571] | 0.654 [0.639, 0.670] |
| ResNet50 | 0.699 [0.686, 0.737] | 0.720 [0.711, 0.935] | 0.635 [0.627, 0.762] | 0.762 [0.500, 0.767] | 0.675 [0.666, 0.839] | 0.763 [0.682, 0.775] | 0.624 [0.601, 0.649] | 0.332 [0.308, 0.360] | 0.439 [0.409, 0.477] | 0.737 [0.720, 0.751] |
| EfficientNet-B0 | 0.694 [0.630, 0.784] | 0.719 [0.711, 0.967] | 0.618 [0.611, 0.788] | 0.767 [0.591, 0.775] | 0.665 [0.658, 0.869] | 0.752 [0.711, 0.887] | 0.583 [0.565, 0.612] | 0.282 [0.256, 0.313] | 0.324 [0.298, 0.358] | 0.789 [0.776, 0.803] |

## Research questions
1. **Best on MURA internal test (by ROC-AUC):** ResNet50 (0.763). ResNet50 vs DenseNet121: difference not distinguishable from 0 (CI includes 0); ResNet50 vs EfficientNet-B0: difference not distinguishable from 0 (CI includes 0). By F1: ResNet50 (0.675); ResNet50 vs DenseNet121: difference not distinguishable from 0 (CI includes 0); ResNet50 vs EfficientNet-B0: difference not distinguishable from 0 (CI includes 0). A model is only described as better where the paired CI excludes zero.
2. **Sensitivity / specificity / F1 / AUC** are given in the table above (the operating point depends on the validation-selected threshold, so compare sensitivity and specificity together, or use ROC-AUC).
3. **Trade-off:** fewest parameters = EfficientNet-B0 (4.3 M); lowest bs=1 latency = ResNet50 (5.7 ms on cuda); shortest training = EfficientNet-B0 (3.3 min). Weigh these against the AUC differences and their CIs; latency depends on hardware.
4. **FracAtlas generalisation (exploratory):** best external ROC-AUC = ResNet50 (0.624). ResNet50 vs DenseNet121: difference not distinguishable from 0 (CI includes 0); ResNet50 vs EfficientNet-B0: 95% CI of the difference excludes 0.
5. **Errors:** FP/FN counts per model and dataset:

| dataset | model | FN | FP |
|---|---|---|---|
| FracAtlas | densenet121 | 331.000 | 1143.000 |
| FracAtlas | efficientnet_b0 | 486.000 | 698.000 |
| FracAtlas | resnet50 | 403.000 | 869.000 |
| MURA test | densenet121 | 406.000 | 529.000 |
| MURA test | efficientnet_b0 | 521.000 | 330.000 |
| MURA test | resnet50 | 498.000 | 337.000 |

Grad-CAM figures: `gradcam/`. Per-image errors: `misclassified/`.

## Label mismatch and domain shift
MURA's *abnormal* label covers fractures but also hardware, degenerative change, lesions and other findings; FracAtlas separates *fractured* from *non-fractured*. Using P(abnormal) as a fracture score on FracAtlas is therefore an exploratory cross-dataset test with imperfectly aligned labels. It is **not** a clinically validated fracture-diagnosis result. Differences in scanner, projection, body-region mix, collection site and image resolution add domain shift (see `domain_shift_image_stats.csv`).

## Limitations
* One training run (single seed) per model; run-to-run variance is not captured by the bootstrap CIs, which reflect test-set sampling only.
* No hyper-parameter search; learning rates were fixed a priori per architecture (documented in `experiment_config.json`).
* MURA image-level labels come from study-level labels; patient-level clustering is only possible if patient ids were recoverable in the audit.
* FracAtlas has no recoverable patient id in the prepared data, so its CIs are image-level.
* The decision threshold is tuned on MURA validation and may not transfer to FracAtlas, where the prevalence and label definition differ.
* Latency was measured on the hardware used here with synthetic input and excludes data loading.
* Grad-CAM shows where the network responds, not clinical correctness.

## Future work
Multiple seeds and ensembling, patient-/study-level aggregation as in MURA's official protocol, body-part-specific models, calibration analysis, external threshold/calibration adaptation using a *separate* held-out FracAtlas split, evaluation on additional external sets, and review of errors by a radiologist.
