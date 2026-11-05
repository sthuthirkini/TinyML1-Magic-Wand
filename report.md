# Magic Wand Gesture Classifier: Version 4 Final Report

## 1. Summary

A 3-class gesture classifier (**W, Z, star**) built in Edge Impulse from a 3-axis accelerometer (ADXL345, sampled at 46 Hz, 3 s windows), targeting an ESP32 via the Arduino IDE.

**Version 4 is the selected model: 90.0% test accuracy (27/30), 88.9% validation accuracy, 21 input features.**

**Known weakness:** the W gesture is still missed at times (80% correct on test; the other errors are one W read as star and one W rated uncertain). 

## 2. V4 configuration

| Block | Setting |
|---|---|
| Sampling | 46 Hz, 3 s window (Nyquist 23 Hz) |
| Scale axes / decimation | 1 / 1 |
| Filter | Low-pass Butterworth, cut-off 6 Hz, order 6 |
| Analysis | FFT, length 16 (bin spacing about 2.9 Hz), log spectrum on, overlap on, improve low-frequency resolution off |
| Input features | 21 (down from 39 with no filter) |
| Network | Dense 20, Dense 10, Output 3 (softmax) |
| Training | 200 cycles, learning rate 0.0005, validation split 20%, batch 32, auto-weight off |
| Deployment target | Quantized (int8), EON Compiler, Arduino library |
| Uncertainty threshold | 0.6 (below this, Studio reports "uncertain") |

## 3. V4 results

**Test set (30 samples, float32)**

| True class | Correct | Misclassified | Uncertain | F1 |
|---|---|---|---|---|
| W | 80% | 10% as star | 10% | 0.89 |
| Z | 90% | 10% as star | 0% | 0.95 |
| Star | 100% | 0% | 0% | 0.91 |

AUC 0.98, weighted precision 0.91, recall 0.90, F1 0.90.

**Validation set (int8):** accuracy 88.9%, loss 0.27, AUC 1.00. The validation set is about 18 samples, so one sample is about 5.6%.

## 4. Experiment log (one change per version)

| Ver | Single change | Test acc | W / Z / Star correct | Uncertain | Decision |
|---|---|---|---|---|---|
| V1 | Baseline: no filter, FFT 16, 100 cycles | 63.3% (19/30) | 60 / 40 / 90 | 9 | Z weak; 94.4% validation was misleading |
| V2 | Low-pass 6 Hz, order 6 | 76.7% (23/30) | 60 / 80 / 90 | 5 | Keep |
| V3 | FFT 16 to 32 | 76.7% (23/30) | 60 / 80 / 90 | 5 | Revert: no gain, AUC 0.98 to 0.95 |
| **V4** | **Cycles 100 to 200 (FFT 16)** | **90.0% (27/30)** | **80 / 90 / 100** | **1** | **Fina
