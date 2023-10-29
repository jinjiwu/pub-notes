
H2-Mapping: Real-time Dense Mapping Using Hierarchical Hybrid
Representation

可以在**边缘计算**的设备上实时构建高质量的地图。

- 层次混合表示。利用隐式多分辨率hash编码，描述不同层次的细节。使用了八叉树sdf先验来帮助构建隐式地图，可以加速地图初始化和学习。
- 最大覆盖关键帧选择策略。解决遗忘问题和提高地图质量。
- 边缘设备运行。

![Overview](assets/doctor/H2-Mapping_fig2.png)

八叉树节点初始化

**Expanded Voxels Allocation** 

当体素顶点sdf接近0时，优化时可能会使sdf符号相反，导致表面不存在。如果多分配一个voxel，优化时总可以保证表面存在。

![Expanded Voxels Allocation](assets/doctor/H2-Mapping_fig4.png)

**Multiresolution Hash Encoding**

学习sdf的残差，而不是完整场景的sdf。

**Coverage-maximizing Keyframe Selection**

$\frac{N_o}{N_c+N_l}$，$N_c$是当前帧观测到的voxel数量，$N_l$是上一帧的voxel数量，$N_o$是共同的voxel数量。

将voxel标记为unobserverd，从keyframe set优先选择覆盖率最高的K keyframes，将其标记为observed，使用同样的策略应用到unobserverd voxel中，选择K keyframes。如果都observed，则重置为unobserved。


