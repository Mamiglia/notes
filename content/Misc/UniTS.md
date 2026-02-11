---
title: UniTS
description: 
date: 18/01/2025
tags:
  - paper
url: https://arxiv.org/abs/2403.00131v3
---
## UniTS: A Unified Multi-Task Time Series Model
UniTS tries to introduce a foundational model for sequential data. They do so by using an adapted version of the transformer architecture and by training with multiple datasets with different tasks.

In order to handle data diversity they first encode patches of the time sequence. Then they use special tokens like GEN or CLS to encode for a specific task (forecasting, classification, ...). Finally they use learned prompt tokens to adapt the model to the specific dataset/task. 


### Architecture
Each block is composed by:
1. **Time MHSA**: self-attention on the sequence length
2. **Variable MHSA**: self-attention on the dimensionality of the tokens (transposed SA).
3. **[DyLinear](https://github.com/mims-harvard/UniTS/blob/0e0281482864017cac8832b2651906ff5375a34e/models/UniTS.py#L79)**: FF that can be stretched to accomodate tokens with varying dimensionality.

### Tasks
UniTS was trained simultaneously on multiple tasks.
- **Forecasting**: they append *GEN* tokens to the end of the sequence to indicate that the model needs to fill them.
- **Imputation**: they mask tokens in the middle of the sequence with *GEN* tokens
- **Classification**: They append one *CLS* token at the end of the sequence. After processing they compare the similarity between the outcome of the *CLS* and learned class embeddings (equivalent to one linear layer).
- **Anomaly Detection**: they use reconstruction error on *GEN* tokens.

### Datasets
UniTS was trained on a diverse set of 38 time series datasets across various domains, including human activity sensors, healthcare, engineering, and finance.

> For our purposes it's important to notice that none of these dataset includes molecular biology data. 

> Also these datasets are characterized by continuous numerical data, such as sensor readings, audio signals, and physiological measurements. They do not include discrete inputs like amino acids, words, or DNA bases.

1. **Blink**: Contains time series data for classifying eye blink events.
2. **MotionSenseHAR**: Includes sensor data for human activity recognition.
3. **EMOPain**: Focuses on classifying emotional pain levels from physiological signals.
4. **SharePriceIncrease**: Contains financial data for predicting stock price increases.
5. **AbnormalHeartbeat**: Includes ECG data for detecting abnormal heartbeats.
6. **AsphaltObstacles**: Contains sensor data for detecting obstacles on asphalt surfaces.
7. **AsphaltObstaclesCoordinates**: Provides coordinates of obstacles detected on asphalt surfaces.
8. **AsphaltPavementType**: Includes data for classifying types of asphalt pavement.
9. **AsphaltPavementTypeCoordinates**: Provides coordinates of different asphalt pavement types.
10. **AsphaltRegularity**: Contains data for assessing the regularity of asphalt surfaces.
11. **AsphaltRegularityCoordinates**: Provides coordinates related to the regularity of asphalt surfaces.
12. **BinaryHeartbeat**: Focuses on binary classification of heartbeat signals.
13. **CatsDogs**: Contains sensor data for distinguishing between cats and dogs.
14. **Colposcopy**: Includes medical imaging data for classifying colposcopy images.
15. **CounterMovementJump**: Contains data for analyzing counter-movement jump performance.
16. **DucksAndGeese**: Focuses on classifying sensor data of ducks and geese.
17. **ElectricDeviceDetection**: Includes data for detecting electric devices based on sensor readings.
18. **Epilepsy2**: Contains EEG data for detecting epileptic seizures.
19. **EyesOpenShut**: Focuses on classifying eye open and shut states from sensor data.
20. **FaultDetectionA**: Includes sensor data for detecting faults in mechanical systems.
21. **FruitFlies**: Contains data for classifying fruit fly species based on sensor readings.
22. **InsectSound**: Focuses on classifying sounds produced by different insect species.
23. **KeplerLightCurves**: Includes astronomical data for classifying light curves from Kepler space telescope observations.
24. **MindReading**: Contains data for classifying mental states based on physiological signals.
25. **MosquitoSound**: Focuses on classifying sounds produced by different mosquito species.
26. **NerveDamage**: Includes data for detecting nerve damage from sensor readings.
27. **RightWhaleCalls**: Contains audio data for classifying right whale calls.
28. **Sleep**: Includes data for classifying sleep stages based on physiological signals.
29. **Tiselac**: Contains data for classifying time series of various activities.
30. **UrbanSound**: Includes urban sound data for classifying different environmental sounds.
31. **WalkingSittingStanding**: Contains sensor data for classifying walking, sitting, and standing activities.
32. **Multivariate2018_ts**: A collection of multivariate time series data for classification tasks.
33. **Univariate2018_ts**: A collection of univariate time series data for classification tasks.


### Models
- **UniTS-ST**: Models trained from scratch for each single task. They prove that UniTS architecture is effective at capturing time-series.
- **UniTS-SUP**: A single model trained simultaneously on all the tasks and datasets. 
- **UniTS-PMT**: A frozen version of UniTS-SUP with the prompt tokens finetuned on the specific task/dataset.
