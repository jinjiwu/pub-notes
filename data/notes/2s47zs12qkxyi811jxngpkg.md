
相机固定,对刚性物体做追踪重建.也可以用在静态场景重建的SLAM中.

![系统架构](assets/doctor/BundleSDF_fig2.png){width=100%}

## 方法

###  Online Pose Graph Optimization

从内存池(SLAM中叫关键帧队列)取 $|\mathcal{P}_{pg}|=K$ 帧,与当前帧 $\mathcal{F}_t$ 构成位姿图 $\mathcal{G}=(\mathcal{V},\mathcal{E})$,即 $\mathcal{V}=\mathcal{F}_t \cup \mathcal{P}_{pg}$. 

位姿图损失函数

$$
\mathcal{L}_{pg}=\omega_s \mathcal{L}_{s}(t)+\sum_{i \in \mathcal{V},j \in \mathcal{V},i \neq j}[\omega_f \mathcal{L}_{f}(i,j)+\omega_p \mathcal{L}_{p}(i,j)] 
$$

$\omega_s,\omega_f, \omega_p$ 经验性的设置为1.

$$
\mathcal{L}_{f}(i,j)=\sum_{(p_m,p_n) \in C_{i,j}} \rho (||\xi_{i}^{-1}p_m-\xi_{j}^{-1}p_n||_2)
$$

$C_{i,j}$是通过网络检测出来的对应关系.

$$
\mathcal{L}_{p}(i,j)=\sum_{p \in I_i} \rho (|n_i(p) \cdot (T_{ij}^{-1} \pi_{D_j}^{-1}(\pi_j(T_{ij}p))-p)|)
$$

$\mathcal{F}^{(i)}$ 中的点在 $\mathcal{F}^{(j)}$ 中的重投影误差.

$$
\mathcal{L}_{s}(t)=\sum_{p \in I_t} \rho (|\Omega (\xi_t^{-1}(\pi_D^{-1}(p)))|)
$$

深度图中点的在网络 $\Omega$ 中查询出来的sdf值为0.

### Neural Object Field

![神经物体场](assets/doctor/BundleSDF_fig3.png){width=100%}

几何函数 $\Omega:x \mapsto s,x \in \mathbb{R}^3,s \in \mathbb{R}$

外观函数 $\Phi:(f_{\Omega(x)},n,d) \mapsto c, f_{\Omega(x)} \in \mathbb{R}^3,n \in \mathbb{R}^3,d \in \mathbb{R}^3,c \in \mathbb{R}_{+}^3$

法线 $n(x)=\frac{\partial \Omega(x)}{\partial x}$

光线ray $x_i(r)=o(r)+t_i d(r)$ $o(r),d(r)$是光线的原点和方向

外观渲染方程

$$\begin{gather*}
c(r)=\int_{z(r)-\lambda}^{z(r)+0.5 \lambda} \omega(x_i) \Phi(f_\Omega(x_i),n(x_i),d(x_i)) \mathrm{d}t \\
\omega(x_i)=\frac{1}{1+e^{-\alpha \Omega(x_i)}}\frac{1}{1+e^{\alpha \Omega(x_i)}}
\end{gather*}$$

$\omega(x_i)$ 是钟形概率密度函数. $\alpha$ 常数, 用来控制概率分布.

采样策略 在**空间占据网格开始边界**到 $z(r)+0.5 \lambda$ 之间采样 $N$ 个点, 再以概率 $\mathcal{N}(z(r),\lambda^2)$ 采样 $N'$ 个点, 最终采样 $N+N'$ 个点.

*Uncertain free space*不确定性空间损失,对于边界处的采样光线,给一个小值.

$$
\mathcal{L}_u=\frac{1}{|\mathcal{X}_u|}\sum_{x \in \mathcal{X}_u}(\Omega(x)-\epsilon)^2
$$

*Empty(free) space* 自由空间损失

$$
\mathcal{L}_e=\frac{1}{|\mathcal{X}_e|}\sum_{x \in \mathcal{X}_e}|\Omega(x)-\lambda|
$$

*Near-surface space* 近表面空间损失

$$\begin{gather*}
\mathcal{L}_{surf}=\frac{1}{|\mathcal{X}_{surf}|}\sum_{x \in \mathcal{X}_{surf}}(\Omega(x)+d_x-d_D)^2 \\
d_x=||x-o(r)||_2 \\
d_D=||\pi^{-1}(z(r))||_2
\end{gather*}$$

*appearance network supervision* 颜色损失

$$
\mathcal{L}_c=\frac{1}{|\mathcal{R}|} \sum_{r \in \mathcal{R}}||\Phi(f_\Omega(x),n(x),d(x))-\bar{c}(r)||_2
$$

*Eikonal regularization* Eikonal 正则化,用来保证网络预测的值为sdf

$$
\mathcal{L}_{eik}=\frac{1}{|\mathcal{X}_{surface}|} \sum_{x \in \mathcal{X}_{surface}}(||\nabla \Omega(x)||_2-1)^2
$$

训练损失函数

$$
\mathcal{L}=\mathcal{L}_{pg}+\lambda_u \mathcal{L}_u+\lambda_e \mathcal{L}_e+\lambda_{surf} \mathcal{L}_{surf}+\lambda_c \mathcal{L}_c+\lambda_{eik} \mathcal{L}_{eik}
$$

可训练参数为resolution hash encoder, $\Omega$ , $\Phi$ , and the object **pose updates** in the tangent space parametrized in Lie Algebra $\Delta\bar{\xi} \in \mathbb{R}^{(|\mathcal{P}-1|\times 6)}$, 第一帧的位姿固定.
