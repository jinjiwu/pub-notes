

- [参考链接1](https://zhuanlan.zhihu.com/p/344938643)
- [参考链接2](https://zhuanlan.zhihu.com/p/364178598)

**重参数化技巧** 分离随机变量的不确定性，使得原先无法求导/梯度传播的中间节点可以求导。

## Contiguous latent variable in VAE

直接建模两个Contiguous variable之间的转换函数 $g$ 是很复杂的一件事，但两个协方差矩阵为对角矩阵的多维高斯分布之间的转换却是很简单的，这也是为什么VAE中采用高斯分布对Latent variable建模。

$$
z=g_{\theta}(\epsilon)=\mu_{\theta}+\sigma_{\theta}\epsilon
$$

## Gumbel softmax trick

- [参考链接](https://zhuanlan.zhihu.com/p/455084077)