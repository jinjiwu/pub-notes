

在多尺度（coarse | fine）上使用特征平面，通过把隐向量投影到三个坐标平面，可以把空间复杂度从边长的立方降低到平方。

![系统架构](assets/doctor/ESLAM_fig2.png){width=100%}

### 采样

从当前的相机位姿 $T_i=\{R_i|t_i\}$ 发出光线，在光线上采样，首先通过分层采样得到 $N_{start}$ 个点，在表面附近通过重要性采样得到 $N_{imp}$ 个点，最终得到采样点集合 ${\\{p_n\\}}_{n=1}^{N=N_s+N_i}$。

 ${\\{p_n\\}}_{n=1}^{N=N_s+N_i}$

### 体渲染

使用StyleSDF的方法可以将sdf值转换为体密度。

$$
\sigma(p_n)=\frac{1}{\beta} \cdot Sigmoid (\frac{-\phi_g(p_n)}{\beta})
$$

$\beta$是可学习参数，用来控制表面边界的锐度。

$$
\begin{gather*}
\omega_n=exp(-\sum_{k=1}^{n-1}\mathbf{\sigma}(p_k))(1-exp(-\pmb{\sigma}(p_n))) \\\\
\boldsymbol{\hat{c}}=\sum_{n=1}^{N}\omega_n \boldsymbol{\phi_a}(p_n) \\\\
\boldsymbol{\hat{d}}=\sum_{n=1}^{N}\omega_n z_n
\end{gather*}
$$

### 损失函数

