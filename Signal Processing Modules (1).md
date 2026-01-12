**Signal Processing Modules**

signal\_processing/  
├── moving\_average/  
│   ├── moving\_average.cpp     \# Implementation of SMA (Simple Moving Average)  
│   └── moving\_average.h       \# Header interface  
│  
├── kalman\_filter/  
│   ├── kalman.cpp             \# Implementation of 1D Kalman Filter  
│   └── kalman.h               \# Header interface  
│  
└── README.md                  \# Module documentation

**Integration with Hardware**

\#include "signal\_processing/kalman\_filter/kalman.h"

// 1\. Initialize Filter (Process Noise, Measurement Noise, Estimation Error)  
KalmanFilter heartRateFilter(0.125, 32.0, 1023.0);

void loop() {  
    // 2\. Read noisy data  
    float raw\_bpm \= sensor.getHeartRate();

    // 3\. Apply Filter  
    float clean\_bpm \= heartRateFilter.updateEstimate(raw\_bpm);

    // 4\. Pass clean data to Edge Intelligence  
    checkThresholds(clean\_bpm);  
}

