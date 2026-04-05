## Fine tuned Result
```
Epoch 4/4
  Train Loss: 0.2026
  Val Loss: 0.2423 | Val Acc: 0.9089
  Time: 228s

Test Accuracy: 0.9103

Classification Report:
              precision    recall  f1-score   support

       human       1.00      1.00      1.00       149
ai_generated       0.86      0.88      0.87       149
      refine       0.88      0.85      0.86       148

    accuracy                           0.91       446
   macro avg       0.91      0.91      0.91       446
weighted avg       0.91      0.91      0.91       446

Confusion Matrix:
[[149   0   0]
 [  0 131  18]
 [  0  22 126]]
```
