# Reading handwritten digits

**2026-09-05 · CLASSIFICATION · Automatically executed · Human review pending**

## Research question

Can pixel intensities distinguish ten handwritten digits, and which digits are confused?

## Results

Random forest was selected using training cross-validation. Its holdout macro_f1 was 0.9661, versus 0.0182 for the dummy baseline; it improved on that baseline on this holdout.

Training macro_f1 was 1.0000. The training/holdout difference is descriptive; it is not an independent estimate of model uncertainty.

pixel_2_5 had the largest mean permutation score drop (0.0249). This measures the fitted model's reliance on a feature, not a causal effect; correlated features can share importance.

The selected model made 12 errors among 360 holdout examples. Inspect the confusion matrix and per-class metrics before relying on overall accuracy.

The strongest absolute Pearson feature correlation in the training data was pixel_0_2 / pixel_7_2 (|r| = 0.938); this suggests checking redundancy, not concluding causality.

## Evaluation design

Stratified random holdout; stratified cross-validation on training rows. Training rows: 1437; final holdout: 360. Seed: 42.

Missing-value imputation, scaling and category encoding are fitted inside each CV training fold. Model selection uses training CV only. The frozen winner and dummy baseline are then scored on the holdout. The baseline is allowed to win.

### Training cross-validation

| Model | macro_f1 | Fold SD |
| --- | --- | --- |
| Random forest | 0.9700 | 0.0050 |
| Neural network (64, 32) | 0.9656 | 0.0036 |
| Logistic regression | 0.9644 | 0.0035 |
| Dummy baseline | 0.0183 | 0.0002 |

Fold standard deviations are descriptive spread, not confidence intervals. The best CV score is selection-optimistic.

### Final holdout

| Model | Metric | Value |
| --- | --- | --- |
| Random forest | accuracy | 0.9667 |
| Random forest | macro_f1 | 0.9661 |
| Random forest | balanced_accuracy | 0.9662 |
| Dummy baseline | accuracy | 0.1000 |
| Dummy baseline | macro_f1 | 0.0182 |
| Dummy baseline | balanced_accuracy | 0.1000 |

Approximate 95% percentile bootstrap interval for macro_f1: **0.9438–0.9805** (200 resamples). Percentile bootstrap on fixed-model holdout predictions; stratified by class for classification. Conditional on this fitted model and split; excludes training/model-selection uncertainty.

## Data provenance

Source: [https://archive.ics.uci.edu/dataset/80/optical+recognition+of+handwritten+digits](https://archive.ics.uci.edu/dataset/80/optical+recognition+of+handwritten+digits)

License: CC BY 4.0. Attribution and source description are in SOURCE.md. The exact analyzed snapshot and its SHA-256 are retained.

## Data quality

| Check | Value |
| --- | --- |
| original rows | 1797 |
| original features | 64 |
| missing cells | 0 |
| missing target rows removed | 0 |
| exact duplicates removed | 0 |
| usable rows | 1797 |

Excluded from predictors: none specified. See data_dictionary.csv for column types, missingness and uniqueness.

## Visual evidence

![Missing values and target distribution](figures/data-quality.png)

Missing values and target distribution.

![Numeric feature distributions; up to six features by training/sample variance](figures/distributions.png)

Numeric feature distributions; up to six features by training/sample variance.

![Feature associations; correlation does not imply causation](figures/correlations.png)

Feature associations; correlation does not imply causation.

![Candidate model comparison; error bars are fold standard deviations, not confidence intervals](figures/model-comparison.png)

Candidate model comparison; error bars are fold standard deviations, not confidence intervals.

![Confusion matrix on the final holdout](figures/confusion-matrix.png)

Confusion matrix on the final holdout.

![Permutation feature importance of the selected model](figures/feature-importance.png)

Permutation feature importance of the selected model.

![Actual handwritten images and the model's predictions](figures/digit-examples.png)

Actual handwritten images and the model's predictions.

## Limitations

This is scikit-learn's 8-by-8 digit benchmark. Writer identities are unavailable in this subset, so random-split performance does not establish accuracy for unseen writers or real photos.

The automated pipeline cannot infer all leakage paths, sampling bias, entity grouping or business meaning. Feature importance is post-hoc and is not used to choose the winner. Any follow-up tuned after inspecting this holdout needs a fresh final evaluation.

## Reproduce

From the repository root, install requirements.txt and run:

```bash
python daily_ds/reproduce.py projects/2026-09-05-digits
```

The command uses the saved snapshot, configuration and dataset specification, and writes to reproduced/. analysis.ipynb provides a readable walkthrough. Numeric results may differ slightly across platforms; software versions and code hashes are recorded.

## Your contribution

This study was generated and executed by an automated agent. It has not been reviewed by Saud. Use LEARNING_NOTES.md to record your own explanation, changed code, new experiment and measured outcome; leave unanswered fields blank.
