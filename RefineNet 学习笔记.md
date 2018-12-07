# RefineNet 学习笔记 

## 0. Abstract

目前深度卷积网络在目标识别和图像分割等问题上表现突出，但频繁的下采样丢失了原图像的信息。我们提出一种 RefineNet 网络，使用残差链接显式将各个下采样层和后面的网络层结合在一起。这样网络高层的语义特征可以直接从底层的卷积层中获得 refine。一个 RefineNet 单元使用残差链接和 identity 映射，对于端对端的训练很有效。我们也介绍了一种链接残差池化，它可以捕获大量背景信息。

## 1. Introduction

语义分割的在这儿任务是给每个像素点分配一个标签，这也被认为是密集分类问题（dense classification problem）。相关的问题如被称为目标解译可以被转换为语义分割。最近的 CNN 网络，如 VGG，ResNet 等在识别中变现很好，但这些方法存在明显的限制当解决密集预测任务如语义分割时，下采样操作使得最终的图像信息损失很大。

一种方式是利用反卷积上采样信息，但这种方式无法恢复底层损失的信息。因此它不能生成准确的高层预测结果。底层信息是准确预测边界和细节的基础。DeepLab 提出 dilated 卷积来在不下采样的基础上扩大感受野。但这种策略有两个限制。第一，需要计算大量的卷积特征图在高纬上而引起（computational expensive）+（huge GPU memory resources）。这限制了计算高层特征和输出尺度只能为输入的 1/8。第二，dilate 卷积引起粗糙下采样特征，这潜在导致重要细节的损失。

另外的方法融合中层特征和高层特征。这种方法基于中层特征保持了空间信息。这种方法尽管补充了特征如边界、角落等，但缺乏强大的空间信息。

所有层的信息对于语义分割都是有帮助的。高层的信息识别图像区域的类别，底层特征参数轮廓、细节边界信息。如何结合这些特征任然是一个开放问题。为了这个目的我们提出 RefineNet 网络， 主要贡献：

1、我们提出多路径 Redinement 网络，融合多层特征。 
2、使用残差级联的方法组织网络。这样梯度可以短路传播。 
3、我们提出链式残差池化来从一张大图区域中捕获背景信息。它通过多窗口尺寸池化特征，再通过残差链接和学习权重方式融合这些特征。 
4、使用残差网络作为提取特征。

### 1.1. Related Work

FCN 
dilated convolution 
CRF-RNN 
deconvolution 
尽管存在各种网络， 但如何有效利用中间层特征仍然是一个开放问题，我们提出 RefineNet 的架构明显和这些网络不同。RefineNet 又许多特殊设计的组件组成，这些组件可以 refine 粗糙的高层语义信息通过融合底层的视觉特征。尤其是，RefineNet 使用长距离和短距离的残差链接使用 identity 映射来优先训练整个端对端系统。

## 2. Background

ResNet -> FCN -> RefineNet



![img](https://img-blog.csdn.net/20171218201233114?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDA2NzM5Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



特别地，我们设置步长为 2，这使得一个块到另一个块特征图减小一半，这样一系列的下采样有两个影响：

- 增加了更深层的感受野，使得过滤器能捕捉到更多的全局信息，这对于高质量的分类是有必要的。
- 使得训练有效、有迹可循因为每个层组合大量的过滤器，使得输出层有合适数量的通道。

尤其最后一层是输入的 1/32 大小，这样小的尺寸损失了大量的信息，这是 CNN 基础方法的普遍限制。

一种可选的方法是 dilate 卷积。下采样操作被移除（stride=1），使用 dilate 卷积。这增加了内存消耗。

## 3. Proposed Method

### 3.1 Multi-Path Refinement

我们使用长残差连接探索多层特征为高分辨预测。RefineNet 提供了一个融合高分辨语义特征和良好获得的低分辨语义特征来生成高分辨分割图。这种设计重要的一个方面是确保梯度可以无效的



![img](https://img-blog.csdn.net/20171218203808260?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDA2NzM5Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



![img](https://img-blog.csdn.net/20171218203624088?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDA2NzM5Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)