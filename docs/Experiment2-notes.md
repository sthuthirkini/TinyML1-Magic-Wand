# Experiment EXP-002: Improved Gesture Classifier

## Objective
Improve performance for the Magic Wand gesture classifier.

## Changes Made

Preprocessing: Impulse includes Spectral Analysis (FFT) **AND** Flatten processing blocks.
-Adding Flatten to processing stages it accounts for the time sequence and retains ordering in its feature vector,which are crucial for gestures, and are not accounted by Spectral Analysis in exp1.
-Include data normalisation.
-All other settings remain same,including the fully connected neural network architecture,
**except for 60 input neutrons(39 from Spectral Analysis and rest from Flatten)**

## Results
Validation accuracy: 94.4%
Validation loss: 0.25
Area under ROC Curve (AUROC): 1.00
Weighted average precision: 0.95
Weighted average recall: 0.94
Weighted average F1 score: 0.94
Model quantization: Int8

## Comparison
- Previous experiment:
- Single change made:
- Metric differences:
- Per-class performance differences:

## Observations
What did the confusion matrix and signal plots reveal?
Observations
-The model achieved 94.4% validation accuracy, compared with 61.1% for Model 1, an improvement of 33.3%,suggesting that Flatten was essential to retain order in data.
-The validation loss was 0.25, indicating lower prediction loss than the recorded Model 1 result, for which the loss was approximately 0.77.
-The AUROC of 1.00 suggests excellent class-separation performance on the validation data under the metric used by Edge Impulse.
-The Flatten feature map showed some separation between Circle and the main W cluster, while W and Z overlapped in several regions.

## Conclusion
What did this experiment establish? What remains uncertain?

-Model 2 achieved 94.4% validation accuracy, substantially improving on Model 1's 61.1%. 
-Circle and Z achieved 100% class-wise accuracy, while W achieved 83.3%, with some W samples misclassified as Circle. 
-Despite these promising results, the small dataset of 90 samples, possible differences in validation splits, overlapping gesture features, and unverified contributions of the Spectral Analysis and Flatten blocks limit the conclusions.
-Training accuracy and independent test accuracy have not yet been established. Therefore, the improvement requires confirmation through testing on unseen data before the model can be considered reliable for real-world gesture recognition.

## Next experiment
What single change will be tested next, and why?
Test the model on data never seen before and measure metrics on test data.
