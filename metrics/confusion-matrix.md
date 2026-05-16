# Confusion Matrix and Metrics

Positive/threat class: **Phishing**  
Negative/non-threat class: **Legitimate**

## Test Summary

- Total held-out test images: **10**
- Phishing test images: **5**
- Legitimate test images: **5**
- Correct predictions: **10 / 10**
- Average confidence: **98.7%**
- Average phishing confidence: **97.8%**
- Average legitimate confidence: **99.6%**

## Confusion Matrix

| | Predicted: Phishing | Predicted: Legitimate |
|---|---:|---:|
| **Actual: Phishing** | TP = 5 | FN = 0 |
| **Actual: Legitimate** | FP = 0 | TN = 5 |

## Calculated Metrics

- **Accuracy:** (5 + 5) / (5 + 5 + 0 + 0) = **100%**
- **Precision:** 5 / (5 + 0) = **100%**
- **Recall:** 5 / (5 + 0) = **100%**
- **F1 Score:** 2 × (1.00 × 1.00) / (1.00 + 1.00) = **100%**

## Interpretation

The Teachable Machine classifier correctly identified all 10 held-out images in this small test set. This produced perfect measured performance: 100% accuracy, 100% precision, 100% recall, and 100% F1. These results are useful for showing that the model learned the two classes, but they should not be treated as production-level evidence because the test set only contains 10 examples. A real phishing detector should be evaluated against a much larger and more diverse set of email screenshots.
