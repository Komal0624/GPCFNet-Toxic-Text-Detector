# GPCFNet — Obscene Text Detector

A multi-modal deep learning system that detects obscure/obfuscated toxic comments (leetspeak, phonetic evasion, misspellings) that standard transformer models like BERT and DistilBERT miss.

**Validation Accuracy: 96.3% | Toxic-class F1: 0.89**

---

## The Problem

Standard toxicity classifiers rely almost entirely on subword tokenization (BERT/DistilBERT), so they fail the moment users disguise offensive language:

| Evasion Tactic | Example | Standard Model | GPCFNet |
|---|---|---|---|
| Leetspeak | `h@te`, `fxckn` | ❌ Missed | ✅ Detected |
| Phonetic substitution | `u bxth` (→ "you bitch") | ❌ Missed | ✅ Detected (0.94 confidence) |
| Character insertion | `i.d.i.o.t`, `s t u p i d` | ❌ Missed | ✅ Detected |
| Repetition | `biiitch` | ❌ Missed | ✅ Detected |

## Architecture

GPCFNet (**G**eometric, **P**honetic, **C**haracter, and **F**eature Fusion Network) runs input text through four parallel pipelines and fuses them with a Transformer encoder:

```
Input Text
    ├── Semantic Pipeline   → DistilBERT (frozen)         → meaning/context
    ├── Character Pipeline  → Char-level CNN               → spelling patterns
    ├── Phonetic Pipeline   → Metaphone algorithm (jellyfish) → how it sounds
    └── Metadata Pipeline   → capitalization/punctuation/digits → writing style
                    ↓
            Fusion Transformer (2-layer encoder)
                    ↓
            Binary Classification Head → Obscene / Not Obscene
```

## Results

| Metric | Value |
|---|---|
| Validation Accuracy | 96.3% |
| Precision (Toxic) | 0.89 |
| Recall (Toxic) | 0.89 |
| F1-Score (Toxic) | 0.89 |
| Best Validation Loss | 0.0901 (Epoch 6, early stopping at Epoch 8) |

Trained on 60,000 sampled comments (+ augmented copies) from the [Jigsaw Toxic Comment Classification dataset](https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge), 90/10 train-validation split.

## Tech Stack

- **PyTorch** — model architecture and training loop
- **Hugging Face Transformers** — DistilBERT semantic encoder
- **Hugging Face Datasets** — data loading, sampling, augmentation
- **jellyfish** — Metaphone phonetic encoding
- **scikit-learn** — evaluation metrics
- **dill** — preprocessor serialization

## Repository Structure

```
gpcfnet-obscene-text-detector/
├── GPCF_Data_Processing_Pipeline.ipynb   # Full training + evaluation notebook
├── Project_Report.pdf                     # Detailed project report
├── requirements.txt
├── README.md
└── LICENSE
```

## Getting Started

1. Open `GPCF_Data_Processing_Pipeline.ipynb` in Google Colab (GPU runtime recommended — Runtime → Change runtime type → T4 GPU).
2. Run all cells top to bottom.
3. Download `train.csv` from the [Jigsaw dataset on Kaggle](https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge/data) and place it where `DataConfig` expects it.
4. Training takes ~20–25 minutes on a T4 GPU (8 epochs with early stopping).

```bash
pip install -r requirements.txt
```

## Key Design Decisions

- **DistilBERT is frozen** during training — only the CharCNN, phonetic embedder, metadata extractor, and fusion transformer are trained. This keeps training fast and computationally cheap without sacrificing semantic quality.
- **Data augmentation** synthetically generates leetspeak, character-repetition, and punctuation-insertion variants of toxic samples, doubling the model's exposure to disguised toxicity.
- **Early stopping** (patience = 2) on validation loss prevents overfitting.

## Limitations & Future Work

- Trained on a 60k-sample subset for compute efficiency; full 159k dataset could improve generalization.
- English-only; a multilingual backbone (e.g., XLM-RoBERTa) would extend coverage.
- Emerging evasion tactics (e.g., homoglyph substitution using Cyrillic characters) aren't yet covered.
- Planned: ONNX export for real-time API deployment.

## Author

**Komal** — B.Tech CSE, Central University of Haryana
Guided by Dr. Nitin Goyal, Assistant Professor, Dept. of CSE

## License

MIT License — see [LICENSE](LICENSE) for details.
