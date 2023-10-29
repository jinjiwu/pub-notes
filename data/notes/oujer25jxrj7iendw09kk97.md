
## inverse CDF sampling

> 使用累积分布函数做变换得到的随即变量服从(0,1)上的均匀分布。反之，如果先从（0,1）上的均匀分布采样y,在做某个累积分布的逆变换，得到的累积分布函数正好是 $F_X(x)$ 

cdf的逆函数: $x=F_X^{-1}(u)=\inf_x\{x:F_X(x)>u\}$

生成cdf为 $F_X(x)$ 样本的过程：

1. 生成 $U \sim U(0,1)$ 的随即变量 $U$。
1. 计算 $X=F_X^{-1}(U)$

即证明 $P(X \leq x)=F_X(x)$ .

$P(X \leq x)=P(F_X^{-1}(U) \leq x)=P(U \leq F_X(x))=F_U(F_X(x))=F_X(x)$

## Rejection sampling



## Importance sampling

## Metropolis-Hastings sampling

## Gibbs sampling


## MCMC sampling
