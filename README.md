# Week 5: AutoML & No-Code Model Training

This lab trained a custom image classifier with Google Teachable Machine and compared a generic Hugging Face model against two fine-tuned models for the **AI-Capstone-Threat-Intel** threat-classification component.

## Custom Model Training

- Built a **Phishing vs. Legitimate** image classifier with Google Teachable Machine.
- Tested the model on **10 held-out screenshots**: 5 phishing and 5 legitimate.
- Achieved **100% accuracy** on the held-out test set.
- Precision: **100%** | Recall: **100%** | F1: **100%**

## Fine-Tuned Model Comparison

Compared 3 models on 5 cybersecurity test inputs:

- Generic: `distilbert/distilbert-base-uncased-finetuned-sst-2-english`
- Fine-Tuned A: `mrm8488/bert-tiny-finetuned-sms-spam-detection`
- Fine-Tuned B: `cardiffnlp/twitter-roberta-base-sentiment-latest`

## Finding

The workflow selected the generic sentiment model as the winner for all 5 records based on raw confidence score. However, the generic model labeled every record as `NEGATIVE`, including routine/benign records, so confidence alone was not a reliable indicator of capstone usefulness.

The strongest practical takeaway is that task-specific model selection matters. The recommended next step is to use or fine-tune a cybersecurity-specific classifier and evaluate it with shared labels such as high concern vs. low concern.

See `report.md` for the full analysis.

## Repository Structure

```text
week-05-automl-training/
├── README.md
├── workflow.json
├── report.md
├── results/
│   ├── comparison-table.csv
│   └── model-comparison-summary.md
├── teachable-machine/
│   ├── screenshots/
│   │   └── README.md
│   └── test-results/
│       └── teachable_machine_test_results.csv
└── metrics/
    ├── confusion-matrix.md
    └── teachable_machine_metrics_final.xlsx
```
