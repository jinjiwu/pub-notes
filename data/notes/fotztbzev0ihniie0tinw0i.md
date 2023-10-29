
invertible compound function

![coupling layer](assets/doctor/NIS_fig1.png){width=100%}

NIS Pipeline

![Pipline](assets/doctor/NIS_fig3.png){width=100%}

**One-blob Encoding**

kernel函数激活多个相邻的值，而不是单个值（one-hot）

假设一个标量值 $s \in [0,1]$ ,区间被划分为k个bin($k = 32$)。将核函数（Gaussian kernel with $\sigma = 1/k,\mu = s$)离散化到bin中，得到s的one-blob encoding。

