[TOC]

# SGD

容易陷入局部最优和随着当前梯度震荡&#x20;

# SGD-M&#x20;

基于历史梯度引入一阶动量，加速收敛（历史梯度累积），减少震荡（削弱了当前梯度的影响）&#x20;

$$
final_score = (1-\alpha) * model\_score + \alpha * profit\_score
$$

本次实验 $\alpha=0.1$ 


```math
final\_score = (1-\alpha)*model\_score+\alpha*(profit\_score)
```

