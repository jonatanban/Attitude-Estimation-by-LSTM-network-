# Attitude Estimation by LSTM network
use  AI (LSTM network) to find and remove the bias and drift , right before the data goes into the Kalman Filter

# ECEF Inertial Navigation and EKF Toolkit

A Python-based toolkit for high-fidelity Inertial Navigation System (INS) processing in Earth-Centered, Earth-Fixed (ECEF) coordinates. This repository provides a complete pipeline for IMU kinematics, coordinate transformations, and an Error-State Extended Kalman Filter (EKF) featuring Zero-Velocity Updates (ZUPT).

## Features

* **Attitude Kinematics:** Full support for Quaternions, Direction Cosine Matrices (DCM), and Euler angles.
* **Coordinate Transformations:** Convert seamlessly between ECEF, Geodetic (Lat/Lon/Alt), and North-East-Down (NED) frames.
* **High-Fidelity Gravity Model:** Computes gravity vectors taking into account Earth's J2, J3, and J4 spherical harmonic perturbations.
* **Inertial Propagation:** Integrates specific force and angular rate data to track position, velocity, and attitude over time.
* **15-State Error EKF:** Tracks and corrects errors in Position, Velocity, Tilt, Accelerometer Bias, and Gyroscope Bias.
* **ZUPT Integration:** Includes Zero-Velocity Update logic to drastically reduce drift in land-based or pedestrian navigation scenarios.

## Dependencies

* `numpy`
* `scipy` (If utilizing matrix square roots in future updates)
* `torch` (If extending to AI-IMU or neural network integrations)

*Note: Ensure your Python environment defaults to `float64` for numerical stability during EKF propagation.*

## Core Modules

* `nav_ecef_to_geod` / `nav_c_ecef_to_ned`: Coordinate frame transformations.
* `nav_inertial_prop`: Propagates the navigation state forward in time using IMU increments.
* `nav_kal_prop`: Predicts the EKF error-state covariance forward.
* `nav_zupt_update`: Applies a Zero-Velocity measurement to compute the Kalman gain and error state.
* `nav_correct`: Applies the EKF error state back into the main navigation state (closed-loop correction).

## Usage Overview

1. Initialize your filter states and covariance using `nav_kal_init`.
2. For every incoming IMU sample, propagate the main state using `nav_inertial_prop`.
3. Compute the system dynamics matrix using `nav_dyn_mat` and propagate the EKF covariance via `nav_kal_prop`.
4. When a zero-velocity event is detected, trigger `nav_zupt_update` to estimate the current state errors.
5. Apply the calculated errors to your main state using `nav_correct` and reset the error vector.
