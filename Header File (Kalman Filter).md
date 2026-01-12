**Header File (Kalman Filter)**  
\#ifndef KALMAN\_H  
\#define KALMAN\_H

class KalmanFilter {  
public:  
    /\*\*  
     \* @brief Constructor for the 1D Kalman Filter  
     \* \* @param process\_noise (Q) \- How much the system state varies (e.g., how fast does HR change?)  
     \* @param sensor\_noise (R) \- How much noise is in the raw sensor reading?  
     \* @param estimated\_error (P) \- Initial guess of how wrong we might be.  
     \* @param intial\_value \- Starting value for the filter.  
     \*/  
    KalmanFilter(double process\_noise, double sensor\_noise, double estimated\_error, double intial\_value);

    /\*\*  
     \* @brief Updates the filter with a new raw measurement  
     \* \* @param measurement The raw, noisy value from the sensor  
     \* @return double The filtered, clean value  
     \*/  
    double updateEstimate(double measurement);

    // Getters for debugging  
    double getKalmanGain() const;  
    double getEstimateError() const;

private:  
    /\* Kalman Filter Variables \*/  
    double \_err\_measure;   // R: Measurement Noise Covariance  
    double \_err\_estimate;  // P: Estimation Error Covariance  
    double \_q;             // Q: Process Noise Covariance  
      
    double \_current\_estimate;  
    double \_last\_estimate;  
    double \_kalman\_gain;   // K: Kalman Gain  
};

\#endif // KALMAN\_H  
 **Source File**  
\#include "kalman.h"  
\#include \<cmath\> // Included just in case advanced math is needed later  
KalmanFilter::KalmanFilter(double process\_noise, double sensor\_noise, double estimated\_error, double intial\_value) {  
    this-\>\_q \= process\_noise;  
    this-\>\_err\_measure \= sensor\_noise;  
    this-\>\_err\_estimate \= estimated\_error;  
    this-\>\_current\_estimate \= intial\_value;  
    this-\>\_last\_estimate \= intial\_value;  
    this-\>\_kalman\_gain \= 0.0;  
}

double KalmanFilter::updateEstimate(double measurement) {  
    // 1\. Prediction Step:  
    // We assume the state hasn't changed much since last time (constant model)  
    // The uncertainty (P) increases because time has passed  
    \_err\_estimate \= \_err\_estimate \+ \_q;

    // 2\. Update Step (Correction):  
    // Calculate Kalman Gain (K)  
    // K \= P / (P \+ R)  
    \_kalman\_gain \= \_err\_estimate / (\_err\_estimate \+ \_err\_measure);

    // Update the estimate using the new measurement and the Gain  
    // Estimate \= Last\_Estimate \+ K \* (Measurement \- Last\_Estimate)  
    \_current\_estimate \= \_last\_estimate \+ \_kalman\_gain \* (measurement \- \_last\_estimate);

    // Update the error covariance (P) for the next cycle  
    // P \= (1 \- K) \* P  
    \_err\_estimate \= (1.0 \- \_kalman\_gain) \* \_err\_estimate;

    // Save state for next loop  
    \_last\_estimate \= \_current\_estimate;

    return \_current\_estimate;  
}

double KalmanFilter::getKalmanGain() const {  
    return \_kalman\_gain;  
}

double KalmanFilter::getEstimateError() const {  
    return \_err\_estimate;   
}

