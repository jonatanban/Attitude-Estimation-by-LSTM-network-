# Hybrid AI-ESKF: Deep Learning Enhanced Inertial Navigation

Traditional Inertial Navigation Systems (INS) suffer from unbounded error growth due to accelerometer bias and gyroscope drift. While an Error-State Kalman Filter (ESKF) with Zero-Velocity Updates (ZUPT) can bound position and tilt errors, azimuth (heading) remains weakly observable in short static alignments.

This repository provides a hybrid Model-Based Deep Learning (MBDL) solution. We train a compact Long Short-Term Memory (LSTM) network to accurately estimate and remove IMU bias and drift from raw sensor windows before the data enters the physics-based ESKF. By delegating the hard-to-observe noise components to the learned estimator, the classical filter achieves much tighter position bounds and prevents azimuth drift.

## Key Features

### AI-Powered Noise Estimation (LSTM)
* Real-time Bias and Drift Tracking: Uses a trained LSTM to isolate static bias and stochastic drift from 0.25-second windows of raw IMU data.
* Pre-Filter Injection: Cleans the IMU signals before kinematic integration, relieving the Kalman Filter from the need to estimate large biases online.

### High-Fidelity Physics Engine (ECEF & EKF)
* Attitude Kinematics: Full, robust support for Quaternions, Direction Cosine Matrices (DCM), and Euler angles.
* Coordinate Transformations: Seamless conversions between ECEF, Geodetic (Lat/Lon/Alt), and local North-East-Down (NED) frames.
* Advanced Gravity Model: Computes gravity vectors incorporating Earth's J2, J3, and J4 spherical harmonic perturbations.
* 15-State Error-State EKF: Tracks position, velocity, tilt, accelerometer bias, and gyroscope drift.
* ZUPT Integration: Implements Zero-Velocity Updates to aggressively bound integration drift during stationary periods.

## Dependencies

* numpy: Core matrix and mathematical operations.
* scipy: Advanced linear algebra routines.
* torch: PyTorch framework for LSTM neural network inference and training.
* plotly: Data visualization for navigation performance and A/B testing.

Note: The navigation physics engine strictly utilizes 64-bit floating-point format (double precision) to ensure numerical stability during kinematic integration.

## Usage Pipeline

1. AI Pre-Processing: Buffer incoming raw IMU data and pass it through the IMUNoiseEstimator (LSTM) to predict the current bias and drift.
2. Signal Correction: Subtract the AI's predicted bias and drift from the raw IMU measurements.
3. Initialization: Initialize the 15-state ESKF and covariance matrices using the kalman initialization function.
4. Kinematic Propagation: Integrate the cleaned specific forces and angular rates to track position, velocity, and attitude.
5. Covariance Prediction: Compute the system dynamics matrix and propagate the error covariance forward in time.
6. ZUPT & Correction: Upon detecting a stationary event, trigger a Zero-Velocity Update to calculate the Kalman gain, then feed the error state back into the main kinematics via closed-loop correction.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/USERNAME/REPOSITORY/blob/main/YOUR_NOTEBOOK.ipynb)
