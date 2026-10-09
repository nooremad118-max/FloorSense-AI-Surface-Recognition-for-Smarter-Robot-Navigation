# FloorSense AI: Surface Recognition for Smarter Robot Navigation

## Overview

FloorSense AI identifies the type of floor a mobile robot is driving on, using only the data from its onboard IMU (inertial measurement unit). Knowing the surface lets the robot's controller adapt its behavior, such as speed and control actions, for safer and smoother navigation indoors.

The project covers the full machine learning workflow: sensor signal processing, feature engineering, model comparison, and validation designed to measure how well the system generalizes to robot runs it has never seen.

## Dataset

**Source:** Kaggle Dataset / Competition: **TAU Robot Surface Detection**
**Link:** [Kaggle: TAU Robot Surface Detection](https://www.kaggle.com/competitions/robotsurface?utm_source=chatgpt.com)

| | |
|---|---|
| **Type** | Supervised Machine Learning – Multiclass Classification |
| **Field** | Robotics, Sensor Processing, and Artificial Intelligence |
| **Dataset** | TAU Robot Surface Detection |

**Description:** The data was recorded by a wheeled robot driven indoors on different floor surfaces. Each sample is a short window of IMU readings with 10 channels (orientation, angular velocity, and linear acceleration) and 128 time steps. The training set contains 1,703 windows from 36 recording sessions covering 9 surface types. Each window is labeled with its surface, and each session (run) has an ID so that windows from the same run can be kept together during validation.

## Approach

1. **Signal processing:** Orientation quaternions were converted into the gravity direction in the sensor frame, removing angle wraparound and run-specific heading. Gyroscope and accelerometer signals had their per-window mean removed to keep the vibration patterns that depend on the floor, and magnitude channels were added.
2. **Feature engineering:** About 359 features were extracted per window: time-domain statistics, frequency-domain features, wavelet energy features, and cross-channel correlations.
3. **Model comparison:** ExtraTrees and LightGBM were compared, along with a blend of the two and a baseline trained on the raw flattened signals.
4. **Leakage-free validation:** All splits were made by recording session, so windows from the same run never appear in both training and testing. Evaluation used repeated session-grouped cross-validation plus a held-out set of unseen sessions.
5. **Label selection:** Surfaces with only one or two recorded sessions cannot be learned or evaluated reliably, so the final model focuses on the five surfaces with enough data: carpet, fine concrete, hard tiles, soft PVC, and soft tiles.

## Result Summary

- LightGBM achieved the highest accuracy of the models compared and was selected as the best model.
- Session-grouped cross-validation: about **75% accuracy** and **0.66 macro-F1**.
- Held-out unseen sessions: about **69% accuracy** and **0.67 macro-F1**.
- Engineered features clearly outperformed the raw-signal baseline.
- Carpet, hard tiles, and fine concrete were recognized reliably, while soft tiles and soft PVC remained the hardest pair to separate.
- The main limit is the number of recorded runs per surface rather than the model type, so recording more runs is the most valuable next step.
