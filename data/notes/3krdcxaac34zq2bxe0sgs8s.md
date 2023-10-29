
Point-NeRF: Point-based Neural Radiance Fields

- 和基于MLP的原版NeRF方法对比。点云模型易扩展、易编辑、模型容量更大。
- 和基于体素储存特征向量的混合场景表达方式对比。明确模型几何信息，采样和训练效率更高。
- 和Splatting方法对比。会更慢，但能携带更多的局部信息，拟合view-dependent的效果更好。
- 和U-Net方法对比。渲染的瓶颈从渲染网格转移到了神经描述子的表达能力上，上限更高。

