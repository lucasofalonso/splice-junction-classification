# Splice-Junction Classification

Comparison of three classifiers — **Logistic Regression**, **MLP**, and **1D CNN** — applied to the task of identifying splice-junction regions in DNA sequences.

## Background

In eukaryotic organisms, pre-mRNA contains both **exons** (retained regions) and **introns** (removed regions). The boundaries between them are called **splice junctions**, and they fall into three classes:

| Class | Description |
|---|---|
| **EI** (Exon→Intron) | Donor site — end of an exon, start of an intron |
| **IE** (Intron→Exon) | Acceptor site — end of an intron, start of an exon |
| **N** (Neither) | No splice junction |

These boundaries follow recognizable nucleotide patterns (e.g., GT at donor sites, AG at acceptor sites), making the problem well-suited for sequence-aware models.

## Dataset

[UCI Molecular Biology (Splice-junction Gene Sequences)](https://archive.ics.uci.edu/dataset/69/molecular+biology+splice+junction+gene+sequences)

- **3,190 samples**, each a fixed window of **60 nucleotide bases**
- 3 classes with slight imbalance → stratified split + F1-macro as primary metric
- Place the downloaded file at `data/splice.data` relative to the repo root

## Repository Structure

```
splice-junction-classification/
├── README.md
├── requirements.txt
├── data/
│   └── .gitkeep          # download splice.data from UCI and place it here
└── notebooks/
    └── main.ipynb
```

## Notebook Overview

| Section | Description |
|---|---|
| 1. Imports | All dependencies |
| 2. EDA | Sequence length check, class distribution |
| 3. One-hot Encoding | CNN (N, 60, 4) vs. classical models (N, 240) |
| 4. Data Splitting | Index-based split with `stratify` — same partition across all models |
| 5. Evaluation Metrics | `evaluate_model()` — classification report, confusion matrix, multiclass ROC |
| 6. Models | Logistic Regression · MLP · 1D CNN |
| 7. Model Comparison | Accuracy and F1-macro side by side |

## Models

**Logistic Regression** — linear baseline using flattened one-hot vectors (240 features). 720 learnable weights.

**MLP** — two hidden layers (128 → 64) with ReLU activations and dropout. 17,603 parameters. Captures non-linear feature interactions that logistic regression cannot.

**1D CNN** — two convolutional layers (32 and 64 filters) with max-pooling, followed by a dense head. Learns local nucleotide motifs directly from the sequence matrix (60 × 4), making it the most architecturally aligned with the problem structure.

## Results

As expected, the 1D CNN achieves the best performance — local pattern detection via convolutional filters is a natural fit for identifying splice-junction motifs in fixed-length sequence windows.

## Setup

```bash
# Clone the repo
git clone https://github.com/lucasofalonso/splice-junction-classification.git
cd splice-junction-classification

# Install dependencies
pip install -r requirements.txt

# Download the dataset from UCI (link above) and place it at:
# data/splice.data

# Launch the notebook
jupyter notebook notebooks/main.ipynb
```

## Requirements

See `requirements.txt`. Main dependencies:

- Python 3.9+
- TensorFlow / Keras
- scikit-learn
- pandas, numpy, matplotlib
