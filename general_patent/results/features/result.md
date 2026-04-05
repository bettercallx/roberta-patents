## Random Forest Baseline 

The Random Forest classifier trained on three features: perplexity (PPL), burstiness, and semantic shift.
Overall test accuracy is 50.0%, substantially below the RoBERTa fine-tuning result of 91.0%. 
Among the three features, AI-generated claims were the most distinguishable, with the highest precision (0.59) and recall (0.64). This suggests that purely AI-generate text exhibits detectable surface level statistical patterns, such as lower perplexity and more uniform sentence structure. 
In contrast, both human written and refined claims proved difficult to classify. The confusion matrix reveals that the model frequently misclassified human and refined claims as each other (51 human samples predicted as refined, 51 refined samples predicted as human), indicating that these two categories share similar statistical profiles under the selected features.

Feature importance analysis showed a relatively even distribution across all three features: burstiness contributed 0.37, followed by perplexity 0.33 and semantic shift 0.30. This suggests that no single feature dominates the classification, and all three capture complementary but insufficient signals.

These results serve two purposes: first, they validate the hypotheses that AI text is more predictable and structurally uniform; second, they demonstrate that 3 features are insufficient for reliable classification, thereby motivating the use of deep learning approaches that can capture linguistic patterns.

## Random Forest Result
Val Accuracy: 0.5044

Test Accuracy: 0.5045

Classification Report:
              precision    recall  f1-score   support

       human       0.48      0.46      0.47       149
ai_generated       0.59      0.64      0.62       149
      refine       0.43      0.41      0.42       148

    accuracy                           0.50       446
   macro avg       0.50      0.50      0.50       446
weighted avg       0.50      0.50      0.50       446

Confusion Matrix:
[[68 30 51]
 [22 96 31]
 [51 36 61]]

Feature Importance:
  ppl: 0.3327
  burstiness: 0.3666
  semantic_shift: 0.3007