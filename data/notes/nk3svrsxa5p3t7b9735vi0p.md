
[[(CVPR 2021)iMAP|pub.doctor.nerf.iMAP]]

[[(CVPR 2022)Nice-SLAM|pub.doctor.nerf.Nice-SLAM]]

Co-SLAM: Joint Coordinate and Sparse Parametric Encodings for Neural Real-Time SLAM

![系统架构](assets/doctor/Co-SLAM_fig3.png){width=100%}

1. 模型表示。联合坐标编码和多分辨率稀疏参数编码，用于实时SLAM。
1. 相机追踪。使用常量运动模型得到当前帧相机位姿的初始位姿。
1. BA。之前的nerf-based slam存储关键帧，然后在所有关键帧中采样，得到光线，然后计算每一条光线上的损失。而co-slam方法存储的是关键帧中的一部分像素（5%），所以它可以更加频繁的添加关键帧，消融实验说明可以提升精度。

## Method

**坐标编码**

1. 输入编码。One-blob编码方式。$\gamma(\mathrm{x})$

**参数化编码**

1. 多分辨率特征网格。$\mathcal{V}_{\alpha}=\{\mathcal{V_{\alpha}^l}\}_{l=1}^{L}$
1. sdf decoder. $f_{\tau}(\gamma(\mathrm{x}),\mathcal{V}_{\alpha}\mathrm(x)) \mapsto (\mathrm{h},s)$
1. color decoder. $f_{\phi}(\gamma(\mathrm{x}),\mathrm{h}) \mapsto \mathrm{c}$

$\theta=\{\alpha,\phi,\tau\}$是可学习参数。

**采样**

1. 均匀采样。在光线上均匀采样$M$个点，计算color和sdf。
1. 深度指导采样。重要性采样对于结果并没有重大的提升，反而会减慢追踪和建图的速度。因此，在near和far之间均匀采样 $M_c$ 个点，在表面附近 $[d-d_s,d+d_s]$ 均匀采样 $M_f$ 个点，$d_s$是小的偏移量。

**损失函数**

常规的损失
$$
\mathcal{L}_{rgb}, \mathcal{L}_{depth}, \mathcal{L}_{sdf}, \mathcal{L}_{fs}
$$

在未被观测的free space空间内，为了防止hash冲突产生噪声，提出了平滑性损失，做为正则化项。

$$
\mathcal{L}_{smooth}=\frac{1}{|\mathcal{G}|}\sum_{x \in \mathcal{G}} \Delta_{x}^{2}+\Delta_{y}^{2}+\Delta_{z}^{2} \\
where \ \Delta_{x,y,z}=\left\|\mathcal{V}_{\alpha}(\mathcal{x}+\epsilon_{x,y,z})-\mathcal{V}_{\alpha}({\mathrm{x})}\right\|_{2}
$$

为了保证实时性，在每一次迭代时随即选择一个区域做正则化。

**相机追踪**

恒速假设。camera-to-world变换 $\mathbf{T}_{wc}=\mathrm{exp}(\xi_t^{\wedge}) \in \mathbb{SE}(3)$

$$
\mathbf{T}_{t}=\mathbf{T}_{t-1} \mathbf{T}_{t-2}^{-1} \mathbf{T}_{t-1}
$$

在当前帧选择$N_t$个像素，通过最小化目标函数来优化相机参数

**BA**

BA特点。经典的稠密slam方法做BA需要使用关键帧的所有像素，而神经slam只需要**稀疏的采样**就可以做BA。然而iMap和NICE-SLAM依旧使用原始的关键帧选择策略（基于信息增益、视觉覆盖），关键帧中只有少数的像素（<10%)参与了后续的联合优化。

关键帧存储策略。Co-SLAM中只存储关键帧中的一部分像素（5%），这使得我们可以**频繁的添加**关键帧，维护一个更大的关键帧数据库。执行global BA时，堆积从关键帧数据库选择$N_g$光线。

优化采用**交替**的策略。对场景参数 $\theta$ 优化 $k_m$次，之后在更新相机位姿参数的累积梯度。由于相机参数只有6个参数，这可以提高相机位姿优化的鲁棒性。

