# Experiment EXP-002: Improved Gesture Classifier

## Objective
Improve performance for the Magic Wand gesture classifier by:

-Adding Flatten to processing stages it accounts for the time sequence and retains ordering in its feature vector,
which are crucial for gestures, and are not accounted by Spectral Analysis in exp1.
-Include data normalisation.
-All other settings remain same,including the fully connected neural network architecture,
**except for 60 input neutrons(39 from Spectral Analysis and rest from Flatten)**

## Baseline configuration
- Model:
- Input window size:
- Input features:
- Preprocessing:
- Hyperparameters:
- Random seed:

## Results
- Training accuracy:
- Validation accuracy:
- Test accuracy:
- Macro precision:
- Macro recall:
- Macro F1-score:
- Inference latency:
- Model size:

## Comparison
- Previous experiment:
- Single change made:
- Metric differences:
- Per-class performance differences:

## Observations
What did the confusion matrix and signal plots reveal?

## Conclusion
What did this experiment establish? What remains uncertain?

## Next experiment
What single change will be tested next, and why?
