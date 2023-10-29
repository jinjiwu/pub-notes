
> However for consistent long-term map building this is not necessary as these movable objects are undesired even when they are temporarily static

通过语义把潜在的动态物体过滤掉，本来应该是缺点，但是换一个角度，也可以说是优点，有预测能力，有助于建立长期一致的地图。

![系统](assets/doctor/Towards%20Real-time%20Semantic%20RGB-D%20SLAM%20in%20Dynamic%20Environments_fig2.png)

- 使用segnet网络做语义分割，训练集为PASCAL VOC，使用单独的线程。
- 使用K-Means对depth聚类，计算每个簇的重投影误差。$r_j=\frac{1}{m}\sum_{i \in c_j}^{m} \rho(||\mathbf{u}_i'-\pi(\mathbf(T_{wc}\mathbf{P_i}||^2)$ ，$P_i$ 和 $\mathbf{u}_i$是orb特征匹配的点
- 关键帧策略和orbslam2相同，对关键帧做语义分割。
- 执行两阶段追踪。使用关键帧做第一阶段追踪找动态点，使用local map做第二阶段追踪。
