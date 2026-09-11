# Results

Actual evaluation output from a training run of GPCFNet on the held-out validation set (6,582 samples).

## Summary Metrics

| Metric | Value |
|---|---|
| Accuracy | 96.1% |
| Precision (Toxic) | 0.87 |
| Recall (Toxic) | 0.90 |
| F1-Score (Toxic) | 0.89 |

## Confusion Matrix

| | Predicted Not Toxic | Predicted Toxic |
|---|---|---|
| **Actual Not Toxic** | 5319 | 151 |
| **Actual Toxic** | 105 | 1007 |

## Qualitative Inference Examples

| Input Text | Evasion Type | Verdict | Confidence |
|---|---|---|---|
| "You are an idiot and I hate you." | Standard | Toxic | 0.9977 |
| "baby! why don't we go on a long drive" | Benign | Not Toxic | 0.0003 |
| "I love this product, it's absolutely amazing!" | Benign | Not Toxic | 0.0025 |
| "love u babe, so much" | Benign | Not Toxic | 0.0012 |
| "hope u fxckn d4e!!" | Leetspeak / Symbol | Toxic | 0.9613 |
| "damn your mama didn't teach you to sck coc ig!" | Leetspeak | Toxic | 0.9967 |
| "btxxh stop lying!" | Misspelling | Toxic | 0.5922 |
| "bbitcchh u de@th yo momma" | Repetition / Leetspeak | Toxic | 0.7483 |

> Note: "u bxth" scored 0.0920 (Not Toxic) in this particular run — borderline phonetic evasion case, flagged as a limitation worth further tuning (see main README's Limitations section).
