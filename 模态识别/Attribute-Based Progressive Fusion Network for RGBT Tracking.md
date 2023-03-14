### Attribute-Based Progressive Fusion Network for RGBT Tracking



RGBT目标跟踪是使可见光和热红外图像相融合，二者优势互补，可以实现更好的目标跟踪性能，如热交叉对可见光图像影响很小，而光照、自然天气雨雪雾霾尘等对热红外图像影响较小。但是RGBT目标跟踪也面临着很多挑战，如目标快速运动（FM）、目标尺度的改变（SV）、遮挡（OCC）、光照变化（IV），热交叉（TC）等。

模型的整体网络架构如下：

![image-20230224104008496](https://github.com/dongdong2061/paper_summary/blob/master/image/image-APFnet.png)

首先，网络的backbone是由来自于VGG-M的第一个三层网络，卷积核大小分别是7x7，5x5，3x3。

#### Attribute-Specific Fusion Branch

基于SKNet设计的网络分支结构，针对于数据集挑战一共设计了五个分支，进行特征提取与融合。每个分支输入都分别是利用5x5,4x4的卷积核进行特征提取。

#### Attribute-Based Aggregation Fusion

首先，数据标签在训练阶段是可获知的，但在验证或测试阶段其值是不可知的，也就是我们不知道在实际跟踪阶段应当激活哪一个融合分支，因此这个模块的作用就是自适应地增强对应的特征，使得获得更具鲁棒性的特征。

#### Attribute-Based Enhanced Fusion

先前的工作利用单个编码器和解码器去模型化在模板帧和搜索帧之间的关系，并不能实现多类自增强和交互式增强。为了解决这个问题，作者提出了利用三个编码器去自增强聚合特征，使用两个编码器去交互式增强解码的特征。同时，为了减少模型复杂度，只采用了单头注意力机制。

#### Three-stage Training Algorithm

训练阶段存在三个问题：

1.如果直接训练整个网络，那么所有类型数据的损失会直接反向传播至所有attribute-specific分支，这显然是有问题的

2.在测试阶段是不知道数据标签的，将会出现的每一帧属于什么attribute是不知道的

3.根据已出现的attribute去增强特征，未出现的attribute则会被抑制

为了解决上述问题，作者提出了一个三阶段训练方法：

1. train all attribute-sprcific fusion branch
   - 首先，这个双流CNN模型通过在imageNet-vid上进行模型参数预训练，包括分支结构、三个卷积层以及连个全连接层
2. train aggregation fusion module
   - 在第二阶段，在固定了第一阶段训练好的各层后，通过随机初始化aggregation fusion module和FC6层，在这个阶段，保存了aggregation fusion module的参数
3. train attribute-based enhancement module
   - 最后，使用所有训练数据训练the enhancement fusion transformer modules以及微调其他modules。在这一步，通过随机初始化the enhancement fusion transformer和最后的全连接层fc6。最终保留整体模型的参数。



、
