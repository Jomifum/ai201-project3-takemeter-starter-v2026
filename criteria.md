## 1. Dataset Size
**Target:** At least 200 labeled examples in total.
**Why this target:** Fine-tuning a transformer model requires a minimum threshold of data to adjust weights effectively without immediate overfitting. 200 provides a viable baseline.

## 2. Class Balance
**Target:** No single label makes up more than 60% of the dataset.
**Why this target:** If one class dominates the dataset, the model will learn to simply predict the majority class to achieve high accuracy, ignoring the nuances of the minority classes.

## 3. `personal_action` Recall
**Target:** Recall for the `personal_action` class must be > 0.75.
**Why this target:** The primary goal of the app is to help users find actionable habits. Missing a valid personal action (false negative) is worse for the user experience than accidentally including a debate post.

## 4. Baseline F1 Beat
**Target:** The fine-tuned macro F1 score must beat the zero-shot baseline macro F1 score by at least 0.05.
**Why this target:** This proves that the fine-tuning process actually learned the specific nuances of our Reddit taxonomy, rather than just relying on the base model's pre-trained vocabulary.

## 5. Confidence Gap
**Target:** The accuracy of the most confident third of predictions must be at least 20% higher than the least confident third.
**Why this target:** This proves the model "knows when it doesn't know." A well-calibrated model should have lower accuracy on borderline posts and high accuracy on posts it flags as confident.