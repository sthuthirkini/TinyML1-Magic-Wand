# Final Model Report: Magic Wand Gesture Classifier (Circle, W, Z)

Edge Impulse project "MW" (ID 1141501) · Target: ESP32 + ADXL345


## 1. Summary

The model separates W from Z well and recognises Circle most of the time, but it generalises poorly: about **58.6% test accuracy** against 83.3% on validation. Z is the weakest class. The main limits are the small, inconsistent dataset and orientation sensitivity in the early data, not the network settings.

## 2. Dataset

| Item | Value |
|---|---|
| Classes | Circle, W, Z |
| Sensor | 3-axis accelerometer (ax, ay, az), ADXL345 on deployment |
| Sample length | 3 s at 46 Hz |
| Orientation | Fixed and consistent across all train and test data (after the fix) |
| Validation | About 18 samples (about 8 Circle, 3 W, 7 Z, derived from f1 scores) |
| Test set | 29 samples (10 Circle, 10 W, 9 Z) |
| Split method | Random 20% validation; no session-based split |
| Training sample counts | Not recorded |

**Data changes (log)**

| Change | Detail |
|---|---|
| Orientation standardised | Earlier recordings mixed wand orientations (ax resting near top and bottom). All data now uses one orientation. |
| Training samples deleted | 3 (2 Circle, 1 Z) |
| Test samples deleted | Z samples that looked like Circles; test set went from 30 to 29 *(IDs to reconcile)* |
| Sample IDs named earlier | Z.751rnmsu, Z.751rnb2j, Z.751rp5tl *(reconcile with the 2 Circle + 1 Z training count above)* |
| Deletion rule | Delete only if the sample is clearly another gesture or a failed recording, decided before looking at predictions |

## 3. Model configuration

| Item | Value |
|---|---|
| Input window | 3000 ms, window increase 1000 ms, zero-padded |
| DSP | Spectral Analysis on ax, ay, az. Flatten block with Average, Min, Max removed in EXP-002 *(current Flatten status unconfirmed)* |
| Network | Keras dense: 20 → 10 → 3 outputs |
| Training | 100 epochs, learning rate 0.0005, default batch size, no dropout, CPU |
| Confidence threshold | 0.60 |
| Random seed | Not set or not recorded |
| Deployed as | Arduino library, EON Compiler, int8 quantized |
| On-device (earlier baseline) | About 1 ms inference, about 16K flash, about 1.5K RAM; final model not re-measured |

## 4. Results

### Validation set

| Metric | Value |
|---|---|
| Accuracy | 83.3% |
| Loss | 0.36 |
| ROC AUC | 0.99 |
| Weighted precision / recall / F1 | 0.88 / 0.83 / 0.84 |

| True class | Circle | W | Z | F1 |
|---|---|---|---|---|
| Circle | 75% | 12.5% | 12.5% | 0.86 |
| W | 0% | 100% | 0% | 0.75 |
| Z | 0% | 14.3% | 85.7% | 0.86 |

### Test set (29 samples)

| Metric | Value |
|---|---|
| Accuracy | 58.62% (17/29) |
| ROC AUC | 0.85 |
| Weighted precision / recall / F1 | 0.76 / 0.62 / 0.62 |
| Macro F1 | 0.62 (mean of 0.72, 0.67, 0.46) |
| Macro recall | 57.8% (mean of 90%, 50%, 33.3%) |

| True class | Circle | W | Z | Uncertain | F1 |
|---|---|---|---|---|---|
| Circle (10) | 90% | 0% | 10% | 0% | 0.72 |
| W (10) | 20% | 50% | 0% | 30% | 0.67 |
| Z (9) | 44.4% | 0% | 33.3% | 22.2% | 0.46 |

Edge Impulse's weighted test metrics appear to exclude uncertain samples, so they do not match a simple average of the per-class values.

## 5. History of experiments

| Run | Change | Validation acc. | Test acc. | Notes |
|---|---|---|---|---|
| EXP-001 | Baseline: Spectral + Flatten, mixed orientation | 94.7% | Circle about 50% (full matrix not recorded) | 60 features |
| EXP-002 | Removed Average, Min, Max from Flatten | 84.2% | 50.0% (30 samples) | Circle absorbed 9 of 20 W and Z |
| EXP-003 | Fixed orientation, new recordings, deleted bad samples | 83.3% | 56.67% (30 samples) → 58.62% (29 samples) | Data and sample set changed, so not a controlled comparison |

The 56.67% to 58.62% rise came from removing one uncertain Z test sample (same 17 correct), not from a better model.

## 6. Observations

- **Generalisation gap:** 83.3% validation against 58.6% test. Validation is a random slice of the same recording sessions, so it overstates performance.
- **Z is the weak class:** only 3 of 9 correct, 4 called Circle, 2 uncertain.
- **W and Z are never confused with each other** in test, so the model separates them well. The errors involve Circle.
- **Circle acts as a catch-all:** 6 of 19 non-Circle test samples were labelled Circle, so its precision is about 0.60. Circle has no uncertain samples, while W and Z have 5 between them. The model is confident on Circle and hesitant on the rest.
- **Feature plot:** most Circle samples form a tight, well-separated cluster. About 8 to 10 Circle samples and a few Z samples sit mixed together at the top left, overlapping W and Z. This points to a small set of samples causing most of the confusion. The explorer shows only three feature dimensions, so apparent overlap may be exaggerated.
- **Orientation matters:** the accelerometer includes gravity, and flipping the wand 180° changes the signal. Mixed orientations in earlier data amplified level-based features such as Average, Min and Max.
- **Small test set:** 29 samples gives roughly ±18 points of uncertainty, and one sample is worth about 3 points, so differences of a few percent are not meaningful.

## 7. Deployment

| Item | Value |
|---|---|
| Board | ESP32 Dev Module |
| Sensor | ADXL345 over I2C (SDA GPIO 21, SCL GPIO 22; CS to 3V3, SDO to GND) |
| Library | Arduino library from Edge Impulse, header `PLEASE_GOD_PLEASE_inferencing.h` |
| Read rate | 46 Hz, 3 s window, axes in order ax, ay, az, units m/s² |
| Trigger | Motion threshold on change between two readings 20 ms apart |
| Live accuracy | Not yet measured |

**Known deployment risks**

- The motion trigger starts recording after movement begins, so the first part of each gesture is missed. Training samples start at the gesture onset, which may reduce live accuracy.
- The model assumes the same wand orientation as training. A flipped wand will break it.
- Axis direction and sign on the ADXL345 should be checked: with the wand in the training pose, ax should read about -9.8 m/s².
- The 0.60 confidence threshold is not applied in the sketch, which prints raw scores.

## 8. Limitations and uncertain points

- Whether the model was retrained after the last deletions *(unconfirmed)*.
- Whether Flatten is still in the impulse *(unconfirmed)*.
- No fixed random seed, so run-to-run differences may be partly noise.
- No full baseline test confusion matrix for EXP-001.
- Training class counts, training accuracy and final on-device size and latency were not recorded.
- Validation is not split by session, and the test set is small.
- Some Circle and Z samples may be mislabelled or poorly recorded (not all reviewed).

## 9. Recommended next steps

1. Retrain after the deletions and re-run Model testing on the frozen test set. Store a project version with the numbers in its name.
2. Review the stray Circle and Z samples in the feature explorer against clean ones, using a fixed deletion rule, and log every ID.
3. Record 15 to 30 more clean samples per class (same orientation, consistent start, gesture completed inside 3 s), on at least two different days.
4. Turn on auto-weight classes, and split validation on a session or date metadata key.
5. If Z is still confused with Circle after that, try the EON Tuner, then a Raw data block with a small 1D CNN (dropout about 0.25).
6. Measure live accuracy on the ESP32, and consider a trigger that captures the gesture onset, such as a rolling buffer.
