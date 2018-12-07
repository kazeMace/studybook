# CNN 图像分割中一些常见的知识点整理
## Residual Connection
Residual Connection的概念来自于Deep residual network(ResNet)这篇文章。首先来介绍一下这篇文章。
我们一般印象是随着网络层数的加深，网络越来越复杂，而复杂的网络更有利于特征的抽取，英雌能够达到更好的效果。而实际情况并不是这样，深度残差网络的设计就是为了克服由于常规的网络层堆叠而导致学习效率降低低，准确率无法有效提升的问题，也称网络退化。这种情况并不是由于过拟合造成的，因为过拟合的情况是在训练集下的效果很好，而在测试集上的效果不好，但是目前的情况是即使在测试集上的准确率也会随着层数的加深降低。

这其中的原因是网络越深，梯度消失的现象就越来越明显，网络的训练效果也不会很好。 但是现在浅层的网络。然而浅层的网络（shallower
 network）又无法提升网络的识别效果，因此ResNet就是解决怎样在加深网络的情况下又防止梯度消失的问题。

这个网络的优点有以下几点：
1.

### Residual block的主要结构（残差块）
将前若干层的数据输出直接跳过多层而引入到后面的数据层的输入部分，后面任何一层向量都含有一部分由其前面的某一层限次能够贡献。



1. inception
2. xception
3. deconvolution
4. 空洞卷积
5. skip connection
6. drop out
7. full convolution
8.  bottleneck layer
9.  uppooling
10. 辅助分支
11. feature map
12. CT值阶段
13. dice Coefficient
14. 假阳性
15. 平衡像素值
16. pooling mask
17. 连通区域分析
18. identity transformation

### 为什么ResNetkeyi 解决随着网络层数加深，准确率下降的问题？
除实验证明外，理论上，如果网络已经达到最优，继续加深网络 
