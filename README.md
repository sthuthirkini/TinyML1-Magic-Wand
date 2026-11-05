# Magic Wand: TinyML Gesture Recognition

Recognises three hand-drawn gestures (**W, Z, star**) from a 3-axis accelerometer, using a small neural network trained in [Edge Impulse](https://edgeimpulse.com) and run on an ESP32.

**Result:** 90% test accuracy (27/30) with 21 input features. See [Results](#results) for caveats.

## Hardware

| Part | Details |
|---|---|
| Microcontroller | ESP32 Dev Module (programmed with Arduino IDE) |
| Sensor | ADXL345 accelerometer (I2C) |
| Wiring | VCC to 3.3V, GND to GND, SDA to GPIO21, SCL to GPIO22, CS to 3.3V, SDO to GND |

## How it works

1. **Sample** x, y, z acceleration at 46 Hz for 3 s (414 values per window).
2. **Filter** with a 6 Hz, order 6 Butterworth low-pass. Hand gestures are slow motions, and the filter removes tremor and sensor noise above them.
3. **Extract features** with an FFT (length 16, log spectrum, overlapping frames) plus time-domain statistics: 21 features in total.
4. **Classify** with a dense network (20, 10 neurons, softmax output), quantized to int8.
5. **Decide:** if the top probability is below 0.6, the result is reported as "uncertain".

## Results

Final model: **Version 4** (FFT 16, 200 training cycles, learning rate 0.0005).

| Class | Test correct | Notes |
|---|---|---|
| W | 80% | One read as star, one uncertain |
| Z | 90% | One read as star |
| Star | 100% | |

Overall: 90.0% test accuracy, 88.9% validation accuracy, AUC 0.98.

Model versions were changed **one variable at a time**:

| Version | Change | Test accuracy |
|---|---|---|
| V1 | Baseline, no filter, 100 cycles | 63.3% |
| V2 | Low-pass 6 Hz | 76.7% |
| V3 | FFT 16 to 32 | 76.7% (reverted) |
| **V4** | **200 cycles** | **90.0%** |
| V5 | 300 cycles | 90.0% |
| V6 | Cut-off 10 Hz | 86.7% |

Full details and interpretation: [docs/V4_final_report.md](docs/V4_final_report.md).

## Limitations

- **W is the weakest class** and is sometimes missed.
- The test set is only 30 samples (one sample = 3.3%), so 90% is uncertain by roughly 75% to 97%.
- The test set was used to compare versions, so the 90% is slightly optimistic.
- Data is from a single user, one device and one mounting orientation.
- Live on-device accuracy: **[add your measured result here]**.

## Run it yourself

1. Install the **esp32 by Espressif Systems** board package in Arduino IDE.
2. Install **Adafruit ADXL345** and **Adafruit Unified Sensor** from the Library Manager.
3. Add the Edge Impulse library: **Sketch > Include Library > Add .ZIP Library** and select `[library zip name].zip`.
4. Open `firmware/magic_wand.ino`, set the first line to your library header (`#include <[project_name]_inferencing.h>`), select your board and port, and upload.
5. Open Serial Monitor at **115200** baud. Wait for "GO!", then draw a gesture for 3 seconds.

**Things that affect live accuracy:** the sensor must be mounted in the same orientation as the training data, readings must be in the same units (m/s²), and the loop must run at a true 46 Hz.

## Possible improvements

- Run feature importance to see which axes and frequency bands matter.
- Add dropout to reduce uncertain W predictions.
- Collect more varied W and Z data across days, speeds and sizes.
- Measure and report live accuracy per gesture.

## Acknowledgements

Built with [Edge Impulse](https://edgeimpulse.com).

## License

MIT License. See the `LICENSE` file for details.
