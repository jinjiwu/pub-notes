
提出和tsdf和nerf的混合场景表达，为重建任务量身定做。

![系统](assets/doctor/Neural_RGB-D_Surface_Reconstruction_fig2.png){width=100%}

### 创新点

1. 基于TSDF的Nerf。原版的nerf是基于体密度的表面表示方式会导致大量的artifacts。
1. 提出了相机位姿refine技术来改进重建效果，预测一个平面形变场，消除<mark>相机畸变和内参估计不准</mark>的问题

> 并不像原版nerf一样基于基于物理的方法，但是可以更好的预测边界，得到更好的重建结果。原版nerf中实际表面的半透明物质可以表示视角依赖的影响，但是在重建任务中，会导致更多的噪声和artifacts。

![结果](assets/doctor/Neural_RGB-D_Surface_Reconstruction_fig3.png){width=100%}

NeRF with Depth： 通过nerf得到的光线的中止点的深度与输入的深度计算L2 Loss。

### 方法

需要使用BundleFusion得到初始的相机位姿。

**颜色权重**

$$
\omega_i=\sigma(\frac{D_i}{tr}) \cdot \sigma(-\frac{D_i}{tr})
$$

bell-shaped函数，在物体表面得到的颜色给最大的权重

$$
\mathbf{C}=\frac{1}{\sum_{i=0}^{K-1}\omega_i}\sum_{i=0}^{K-1}\omega_i \cdot \mathbf{c}_i
$$

### 损失函数

通过sdf直接构建损失函数

$$
\begin{gather*} 
\mathcal{L}_{rgb}^b(\mathcal{P})=\frac{1}{|P_b|}\sum_{p \in P_b}(C_p-\hat{C}_p)^2 \\
\mathcal{L}_{fs}^b(\mathcal{P})=\frac{1}{|P_b|}\sum_{p \in P_b}\frac{1}{|S_p^{fs}|}\sum_{s \in S_p^{fs}}(D_s-tr)^2 \\
\mathcal{L}_{tr}^b(\mathcal{P})=\frac{1}{P_b}\sum_{p \in P_b} \frac{1}{|S_p^{tr}|} \sum_{s \in S_p^{tr}}(D_s-\hat{D_s})^2
\end{gather*}
$$

预测带符号的距离场
