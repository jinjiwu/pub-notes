
[[(CVPR 2022)Nice-SLAM|pub.doctor.nerf.Nice-SLAM]]

### 创新点 

1. 使用三维模型的<span style="color:red">混合</span>表达方式
1. 使用八叉树来组织体素模型
1. 基本实时，大概1-2 fps
1. 使用了nerf强大的体渲染表达

## 不足

1. 不能处理动态物体，未使用BA

## 方法
### 网络结构

![网络结构](assets/doctor/Vox-fusin_fig2.png){width=100%}

### 渲染方程

$$ 
\begin{gather*} 
（\mathbf{c}_i,s_i)=F_{\theta}(\mathrm{TriLerp}(\hat{\xi_i}T_i \mathbf{p}_i,\mathbf{e})), \\
w_i=\sigma(\frac{s_i}{tr}) \cdot \sigma(-\frac{s_i}{tr}), \\
\mathbf{C}=\frac{1}{\sum^{N-1}_{j=0} w_i} \sum^{N-1}_{j=0}w_j c_j, \\
D=\frac{1}{\sum^{N-1}_{j=0} w_i} \sum_{j=0}^{N-1}w_j d_j
\end{gather*}
$$

### 损失函数

$$
\begin{gather*} 
\mathscr{L}_{rgb}=\frac{1}{|P|}\sum_{i=0}^{|P|}||\mathbf{C_i}-\mathbf{C}_i^{gt}|| \\
\mathscr{L}_{depth}=\frac{1}{|P|}\sum_{i=0}^{|P|}||D_i-D_i^{gt}||, \\
\mathscr{L}_{fs}=\frac{1}{|P|}\sum_{p \in P} \frac{1}{S_p^{fs}} \sum_{s \in S_p}^{fs}(D_s-tr)^2,\\
\mathscr{L}_{sdf}=\frac{1}{|P|}\sum_{p \in P} \frac{1}{S_p^{tr}} \sum_{s \in S_p^{tr}}(D_s-D_s^{gt})^2
\end{gather*}
$$ 

## 改进优点

1. 相比于Refusion, 可以得到无限分辨率的三维模型.多了BA模块,可以得到全局一致(去掉动态物体)的模型.
1. 相比于先使用网络分割的方法，可以不依赖与任何先验知识，可以根据场景的运动，自动识别动态场景。
1. 扩展了Vox-fusion的使用场景，使之nerf可以用到动态场景的三维重建中。

假设是更相信当前的结果，通过当前的结果来优化过去的结果。

## 改进算法

KeyFrame队列中存储的应该是原始图片 $\{I_k,D_k,M_k\}$. 可以考虑给定第一帧的动态物体掩码。

渲染网格状态：free space网格Vf、TSDF区域内的网格Vt，表面的网格Vs，未分配的网格Vu。

动态物体网格状态变化：Vt->Vf, Vs->Vf. 如果是通过变化得到的Vf,那么不应该参与后续的优化。

- Vf网格约束：多个视角光线下看到的该网格sdf是截断距离。
- Vt网格约束：多个视角光线下看到的该网格的sdf是该角度下的符号距离。
- Vs网格约束：多个视角光线下看到的该网格的sdf=0

对于Vf的网络不做处理。只遍历Vt和Vs的网格.

- 如果是静态网格,那么不同帧之间测到的同一个网格的sdf应该和gt-d是一致的。
- 如果是动态网格,那么每一个网格中所有穿过的光线的sdf和gt-d的差值是不一致的。

如果一个网格中不一致的光线数量很多，那么该特征网格就是不可靠的，是动态的，相应的要设置KF中的mask，然后做洪水填充算法，下次可以在mask之外的区域采样光线。

---

**INPUT**: Volume embeding $e=\{e_i | v_i \in V\}$ , Decode network $F_{\theta}$, KeyFrame Quene Pose $T=\{T_i\}$, rgb assets $I=\{I_i\}$, depth assets $D=\{D_i\}$ 

**OUTPUT**: 优化后的 $e$, $F_{\theta}$, $T$

从KF中的图片中采样光线，得到采样光线集合 $R$. <br>
记录每一个特征网格被采样光线 $r_i \in R$ 穿过,记为 $vr=\{r_i\}$ <br>
for $vr \in V$ <br>
&emsp; for $r \in vr$ <br>
&emsp; &emsp; 计算是否满足 $|d_s+sdf_{pred}*trunc-d_{gt}|<k*trunc$ <br>
&emsp; 得到不一致光线的数量 $n$ <br>
&emsp; if n>N <br>
&emsp; &emsp; 修改八叉树,标记该网格为动态网格,Vt->Vf, Vs->Vf.强制将Vf中网格的特征向量解码出来的sdf变为trunc,或者将网格设置为透明,不参与光线的求交,自然也不会参与采样等后续计算. <br>
&emsp; &emsp; 修改关键帧,使用填充算法,去掉关键帧中的动态区域 <br>

---


