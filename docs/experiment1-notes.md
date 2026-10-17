# Experiment EXP-001: Baseline Gesture Classifier

## Objective
Establish a baseline performance for the Magic Wand gesture classifier.

## Dataset
- Dataset source:ADXL345 accelerometer reading (along x,y and z axis)sper class recorded using Edge Impulse Data Forwarder
- Gesture classes:Circle, W,Z
- Number of recordings and samples:30
- Train / validation / test split:
- Split method:

## Baseline configuration
- Model:Fully connected neural network (
- Input window size:3s
- Input features:39 spectral features derived from accelerometer axes ax, ay, and az
- Preprocessing:Spectral Analysis using FFT; decimation ratio 1; no additional filter
- Hyperparameters:
  Hidden layer 1: 20 neurons
  Hidden layer 2: 10 neurons
  Output layer: 3 classes (Circle, W, Z)
  Training cycles: 100
  Validation split: 20%
  Batch size: Default Edge Impulse setting
  Class weighting: Automatic
  Quantization/profile: Int8 model profiling enabled
-Random Seed:


## Results
- Training accuracy:not provided by edge impulse
- Validation accuracy:61.1%
- Test accuracy:not measured yet
- Area under ROC Curve :	0.82
-Weighted average Precision :	0.62
-Weighted average Recall: 	0.61
-Weighted average F1 score :	0.61

## Observations
What did the confusion matrix and signal plots reveal?
-The model achieved 61.1% validation accuracy across Circle, W, and Z.
- Circle: 62.5% class-wise accuracy.
- W: 66.7% class-wise accuracy.
- Z: 50% class-wise accuracy.
-The key finding was that Z was the hardest gesture to recognize.
Circle and Z appeared to be confused with each other.
-This means the sensor patterns produced by some Circle and Z movements were likely similar enough that
the model struggled to distinguish them.
-W performed slightly better, but its recognition was not reliable either.

The feature plots suggested that:
- The gestures had some distinguishable patterns, so the sensor data contained useful information.
- The classes overlapped in the feature space, particularly Circle and Z, making classification difficult.
- The movements may not have been consistent enough across samples.
- Small differences in speed, direction, tilt, and starting position can change the accelerometer readings.
- Because the wand uses only the three accelerometer axes — ax, ay, and az — the model may not capture every movement detail needed to separate similar gestures.

## Conclusion
What did this experiment establish? What remains uncertain?

This experiment established that  with 90 samples (30 per gesture), the model achieved 61.1% validation accuracy,
indicating that it learned some useful movement patterns 
but did not yet classify the gestures reliably.

The feature plots suggested overlap between gesture classes, particularly Circle and Z.

It remains uncertain whether the limited accuracy is mainly due to insufficient training data, 
variations in gesture execution, or the selected feature-extraction method. 

Performance on completely unseen real-world gestures has also not yet been established.



## Next experiment
Single change to test: Replace Spectral Analysis with Flatten as the processing block.

Keep the dataset, gesture labels, window size, classifier architecture unchanged. 
Train the model using Flatten and compare its validation accuracy, loss, confusion matrix, and per-class performance against the Spectral Analysis baseline.
