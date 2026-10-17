# Magic Wand — TinyML Test Report

## Model Configuration
- **Hardware:** ESP32 + ADXL345 accelerometer
- **Input axes:** `ax`, `ay`, `az`
- **Sampling frequency:** ~46 Hz
- **Window size:** 3 seconds
- **Gesture classes:** Circle, W, Z
- **Classifier:** Neural network (20 → 10 → 3 neurons)
- **Training cycles:** 100

## Results

| Metric | Result |
|---|---:|
| Validation accuracy | 94.7% |
| Validation loss | 0.27 |
| Test accuracy | 60% |
| Test AUROC | 0.86 |
| Weighted F1-score | 0.71 |

### Class-wise Test Accuracy

| Gesture | Correct predictions |
|---|---:|
| Circle | 50% |
| W | 70% |
| Z | 60% |

Circle samples were also marked uncertain 40% of the time, with 10% classified as Z and 0% as W.

## Mistakes and Limitations

- **Inconsistent recordings:** Circle gestures were not always performed consistently. Some recordings involved different movement patterns, and one sample contained two circles.
- **Inconsistent gesture execution:** Differences in movement speed, direction, orientation, and starting/ending positions may have made the same gesture look different to the model.
- **Test-set changes:** Some test samples were removed or replaced during debugging, making the final test accuracy unreliable as an independent benchmark.
- **Limited dataset:** The dataset was relatively small, with approximately 35 Circle, 30 W, and 30 Z training samples.
- **Validation–test gap:** High validation accuracy (94.7%) but lower test accuracy (60%) suggests poor generalization or differences between training and test recordings. The exact cause has not been established.

## Conclusion

The model successfully trained and is suitable for an initial ESP32 deployment trial.
However, its current test performance indicates that gesture recognition needs further evaluation. 
More consistent gesture collection and a fresh, untouched test set are recommended before claiming reliable real-world accuracy.
