
VINS-Mono: A Robust and Versatile Monocular Visual-Inertial State EstimatorS

## Overview

相比于OKVIS：
- 单目。OKVIS是多目
- 单目初始化、关键帧选择策略、大视角相机
- 闭环检测、位姿图重使用

### Measurement Preprocessing

*A. Vision Processing Front End*

- 特征点追踪。使用KLT稀疏光流法追踪特征点
- 角点检测。每张图最大（100-300）特征点
- 外点拒绝。基础矩阵RANSAC模型

关键帧选择

- 视差。计算与前一帧的平均视差。平移和旋转都会影响视差。视差越大，越有可能是关键帧。只有旋转时，不能通过三角化计算视差，此时使用陀螺仪短期积分替代，此积分只用在关键帧选择中。
- 追踪质量。追踪特征下降到一个阈值，则该帧为关键帧。

*B. IMU Preintegration*

### Estimator Initialization

*A. Vision-Only SfM in Sliding Window*

*B. Visual-Inertial Alignment*

1. Gyroscope Bias Calibration
1. Velocity, Gravity Vector, and Metric Scale Initialization
1. Gravity Refinement.
1. Completing Initialization

- 视觉残差的自由度是2

