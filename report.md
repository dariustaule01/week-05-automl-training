# Week 5 Report: AutoML Training & Fine-Tuned Model Evaluation

**Name:** Darius Taule  
**Date:** May 16, 2026  
**Capstone Project:** AI-Capstone-Threat-Intel  
**My Component:** Threat classification and model-comparison workflow

## Part A: Teachable Machine Training

### Training Setup

- **Task:** Phishing vs. legitimate email screenshot classification
- **Tool:** Google Teachable Machine, standard image model
- **Training images per class:** 25 phishing images and 25 legitimate images
- **Test images per class:** 5 phishing images and 5 legitimate images
- **Total test images:** 10 held-out images
- **Total training time:** Not recorded in the uploaded workbook; enter your observed training time if needed.

### Test Results

| # | File Name | Actual Class | Predicted Class | Confidence | Correct? |
|---:|---|---|---|---:|---|
| 1 | test_phish_01.png | Phishing | Phishing | 100% | Yes |
| 2 | test_phish_02.png | Phishing | Phishing | 96% | Yes |
| 3 | test_phish_03.png | Phishing | Phishing | 100% | Yes |
| 4 | test_phish_04.png | Phishing | Phishing | 99% | Yes |
| 5 | test_phish_05.png | Phishing | Phishing | 94% | Yes |
| 6 | test_legit_01.png | Legitimate | Legitimate | 100% | Yes |
| 7 | test_legit_02.png | Legitimate | Legitimate | 100% | Yes |
| 8 | test_legit_03.png | Legitimate | Legitimate | 100% | Yes |
| 9 | test_legit_04.png | Legitimate | Legitimate | 99% | Yes |
| 10 | test_legit_05.png | Legitimate | Legitimate | 99% | Yes |

### Confusion Matrix

| | Predicted: Phishing | Predicted: Legitimate |
|---|---:|---:|
| **Actual: Phishing** | TP = 5 | FN = 0 |
| **Actual: Legitimate** | FP = 0 | TN = 5 |

### Calculated Metrics

- **Accuracy:** (5 + 5) / (5 + 5 + 0 + 0) = **100%**
- **Precision:** 5 / (5 + 0) = **100%**
- **Recall:** 5 / (5 + 0) = **100%**
- **F1 Score:** 2 × (1.00 × 1.00) / (1.00 + 1.00) = **100%**
- **Average confidence:** **98.7%**

### Interpretation

The Teachable Machine classifier correctly predicted all 10 held-out test images. Since the threat class was defined as **Phishing**, this means the model caught every phishing example and did not falsely flag any legitimate screenshots as phishing. The resulting measured accuracy, precision, recall, and F1 score were all 100%.

These results show that the model learned the difference between the two image categories in this small lab dataset. However, the evaluation set only contained 10 images, so the results should be treated as a limited proof of concept rather than a production-ready performance estimate. A real phishing-image classifier would need a larger and more diverse dataset, including screenshots from different email clients, different brands, different screen resolutions, mobile emails, newsletters, receipts, and more sophisticated phishing examples.

For a cybersecurity workflow, **false negatives** would be more costly than false positives because missing a real phishing screenshot could allow a threat to avoid review. Because of that, the priority metric for this component should be **recall**, followed closely by precision to avoid overwhelming analysts with false alerts.

---

## Part B: Generic vs. Fine-Tuned Model Comparison

### Models Tested

1. **Generic:** `distilbert/distilbert-base-uncased-finetuned-sst-2-english` — general sentiment classification
2. **Fine-Tuned A:** `mrm8488/bert-tiny-finetuned-sms-spam-detection` — spam/ham style text classification
3. **Fine-Tuned B:** `cardiffnlp/twitter-roberta-base-sentiment-latest` — negative/neutral/positive sentiment classification

### Results

| # | Input | Generic Label (Score) | Fine-Tuned A Label (Score) | Fine-Tuned B Label (Score) | Workflow Winner |
|---:|---|---|---|---|---|
| 1 | Unauthorized login from IP 198.51.100.4 traced to Moscow targeting user John Miller at 3:47 AM | NEGATIVE (0.9961) | LABEL_0 (0.7045) | neutral (0.8441) | Generic |
| 2 | Routine firewall rule update completed on fw-01 during scheduled maintenance window | NEGATIVE (0.9986) | LABEL_1 (0.5376) | neutral (0.8743) | Generic |
| 3 | Phishing email with spoofed Amazon domain detected targeting finance@acmecorp.com | NEGATIVE (0.9959) | LABEL_0 (0.7415) | negative (0.5479) | Generic |
| 4 | Multiple failed SSH attempts from Beijing IP on Cloudflare production server — 47 attempts in 5 minutes | NEGATIVE (0.9994) | LABEL_1 (0.5719) | negative (0.8257) | Generic |
| 5 | System resource utilization normal across all monitored hosts — no anomalies detected | NEGATIVE (0.9880) | LABEL_0 (0.8388) | neutral (0.6699) | Generic |

### Summary Statistics

| Model | Average Score | Output Pattern |
|---|---:|---|
| Generic DistilBERT SST-2 | 0.9956 | NEGATIVE: 5 |
| Fine-Tuned A: SMS Spam Detection | 0.6789 | LABEL_0: 3, LABEL_1: 2 |
| Fine-Tuned B: CardiffNLP Sentiment | 0.7524 | neutral: 3, negative: 2 |

### Analysis

**Generic model strengths:** The generic DistilBERT sentiment model returned very high confidence scores on every record. It consistently recognized that security-related language sounded negative or concerning, which makes it useful as a simple baseline.

**Generic model weaknesses:** The generic model labeled every record as `NEGATIVE`, including routine firewall maintenance and normal system utilization. This means it did not separate high-concern threat records from benign operational records. Its high confidence should not be treated as proof that it is the best model for the capstone because sentiment confidence is not the same thing as threat-classification accuracy.

**Fine-tuned model advantage:** The fine-tuned models were more informative because they used different label spaces. Fine-Tuned B separated some obviously concerning records into `negative` while assigning routine records to `neutral`. Fine-Tuned A was more domain-adjacent because spam detection is closer to phishing than generic sentiment, but the output labels `LABEL_0` and `LABEL_1` require manual mapping before they can be used safely.

**Biggest surprise:** The workflow winner was `Generic` for all five records because the workflow selected the model with the highest raw confidence score. This is misleading because models with different output labels and calibration should not be compared only by confidence. The generic model was confident, but not necessarily more useful for the actual cybersecurity task.

### Recommended Model for My Capstone Component

**Component:** Threat classification and model-comparison workflow  
**Recommended model from this test:** Fine-Tuned B, `cardiffnlp/twitter-roberta-base-sentiment-latest`, as the most interpretable comparison model among the two fine-tuned options.  
**Reason:** It produced a more meaningful distinction between negative threat-like language and neutral routine language than the generic model, which marked every input as negative.  
**Confidence threshold:** I would use approximately **0.80** as the threshold for automatic routing. Records below that threshold should go to human review.  
**Priority metric:** **Recall** should be the top priority because missing real threats is more costly than creating some false alarms. Precision still matters because too many false positives would waste analyst time.

The best long-term solution would be to fine-tune or select a cybersecurity-specific text classifier trained on phishing, suspicious activity, incident, and benign operational records. The tested models are useful for the lab comparison, but they are not enough by themselves for production threat triage.

---

## Limitations & Next Steps

The Teachable Machine model performed perfectly on the small test set, but 10 images is not enough to prove generalization. The image classifier should be tested on at least 100 held-out screenshots before being trusted. The text model comparison also has a limitation: the workflow compared raw confidence scores across models that use different labels and different calibration. In a stronger version of this workflow, I would add ground-truth labels, convert each model's output into a shared high-concern/low-concern format, and calculate accuracy, precision, recall, and F1 for each text model.

Next steps would be to collect more real phishing and legitimate screenshots, evaluate the image model with a larger test set, test cybersecurity-specific Hugging Face models, and add confidence-based routing in n8n so high-confidence threats are automatically flagged while ambiguous records are sent to human review.
