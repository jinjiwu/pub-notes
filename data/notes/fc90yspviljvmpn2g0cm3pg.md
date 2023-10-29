
Instant neural graphics primitives with a multiresolution hash encoding

![overview](assets/doctor/iNGP_fig3.png){width=100%}

> Pareto optimum: 

## Advantages
1. 

## Background

**高维空间编码** 
- 将输入编码到高维空间，可以使得数据线性可分。可以使用one-hot和kernel trick
- 输入编码对循环神经网络中的注意力机制也有用

**输入编码方法**
1. frequency encodings. 
1. one-blob encoding. 
1. parametric encodings. 将可训练的参数放在辅助的数据结构中，空间换时间。
    - tree。 
        - 自适应性。
        - 引入了额外的计算成本。
    - dense grid。
        - 浪费空间。
        - coarse-to-fine。由于自然场景的平滑性，不同的情况使用不同的level.
1. sparse parametric encodings.
    - hash grid

## Method
1. 哈希函数。空间哈希函数
1. Implicit hash collision resolution. 
    - 不同level同时发生碰撞的概率很低
    - 不同点的对梯度贡献的权值也不一样

##  Accelerated NERF Ray Marching

1. 步长指数增长。对于大的场景，光线上采样点的数量是对数增长，基本不会造成计算量
1. 占据网格。使用Occupancy Grids跳过空格子。
    - 使用。有x和步长delta确定查询第几个网格（网格边长大于x的最小网格）
    - 更新。训练过程中更新占据网格，每16次训练更新一次。额外使用相同结构的网格记录密度值
        - 网格单元中密度值以0.95衰减
        - 随机采样M个候选单元，密度值设为当前值和nerf密度值的最大值
        - 通过阈值t=0.01*1024*sqrt(3)更新占据网格。
1. 数据压缩。将采样数据压缩到GPU缓存中，高效的执行。
