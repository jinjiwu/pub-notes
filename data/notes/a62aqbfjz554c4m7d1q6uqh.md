
Factored Neural Representation for Scene Understanding

## Overview

![pipline](assets/doctor/FactoredNeRF_fig3.png)

## Contributions

## Method 

![Network](assets/doctor/FactoredNeRF_fig5.png)

### Initializations

- [1] Fast Online Object Tracking and Segmentation: A Unifying Approach
- [2] Raft: Recurrent all-pairs field transforms for
optical flow

使用现有的视觉追踪算法[1]做关键帧的语义标注（实例分割和AABB），然后使用光流算法[2]做关键帧之间的匹配，得到关键帧之间的相对位姿，然后使用BA算法优化关键帧的位姿和语义标注。

### Joint optimization


#### Free Space Loss

- [1]Space-time neural irradiance fields for free-viewpoint video

$$
\mathcal{L}_{free}(\mathcal{F})=\sum_{p \in P_{free}} |T_p \alpha_p| / |P_{free}| \\
where \quad P_{free} = \{p(s,r)|s < D_t(r)\}
$$

#### Non-rigid deformation

- [1] Neural surface reconstruction of dynamic scenes with monocular rgb-d camera

