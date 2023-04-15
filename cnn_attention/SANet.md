## [SA-Net: Shuffle Attention for Deep Convolutional Neural Networks](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2102.00240)

**作者**：Qing-Long Zhang, Yu-Bin Yang

**单位**：南京大学计算机软件新技术国家重点实验室

**文章简介**：这是一篇来自于ICASSP 2021的一篇顶会论文，由南京大学杨育彬等人提出了一种新的注意力机制：置换注意力机制（shuffle attention）。它在空域注意力（Spatial Attention）与通道注意力（Channel Attention）的基础上，引入了特征分组与通道置换，得到了一种超轻量型的注意力机制。所提方案在ImageNet与MS-COCO数据集上均取得了优于SE、SGE等性能，同时具有更低的计算复杂度和参数量。

注意力机制已成为提升CNN性能的一个重要模块，一般来说，常用注意力机制有两种类型：spatial attention与channel attention，它们分别从pixel与channel层面进行注意力机制探索。尽管两者组合(比如BAM、CBAM)可以获得更好的性能，然而它不可避免的会导致计算量的提升。

本文提出了一种高效置换注意力(Shuffle Attention，SA)模块以解决上述问题，它采用置换单元高效组合上述两种类型的注意力机制。**具体的说，SA首先将输入沿着通道维度拆分为多组，然后对每一组特征词用置换单元刻画特征在空域与通道维度上的依赖性，最后所有特征进行集成并通过通道置换操作进行组件特征通信。**所提SA模块计算高效且有效，以ResNet50为蓝本，其参数增加为300(基准为25.56M)，计算量增加为2.76e-3GFLOPs(基准为4.12GFLOPs)，而top1精度提升则高达1.34%。

![image-20230314164815598](https://github.com/dongdong2061/paper_summary/blob/master/image/sanet.png)

```python
class sa_layer(nn.Module):
    """Constructs a Channel Spatial Group module.
    Args:
        k_size: Adaptive selection of kernel size
    """

    def __init__(self, channel, groups=64):
        super(sa_layer, self).__init__()
        self.groups = groups
        self.avg_pool = nn.AdaptiveAvgPool2d(1)
        self.cweight = Parameter(torch.zeros(1, channel // (2 * groups), 1, 1))
        self.cbias = Parameter(torch.ones(1, channel // (2 * groups), 1, 1))
        self.sweight = Parameter(torch.zeros(1, channel // (2 * groups), 1, 1))
        self.sbias = Parameter(torch.ones(1, channel // (2 * groups), 1, 1))

        self.sigmoid = nn.Sigmoid()
        self.gn = nn.GroupNorm(channel // (2 * groups), channel // (2 * groups))

    @staticmethod
    def channel_shuffle(x, groups):
        b, c, h, w = x.shape

        x = x.reshape(b, groups, -1, h, w)
        x = x.permute(0, 2, 1, 3, 4)

        # flatten
        x = x.reshape(b, -1, h, w)

        return x

    def forward(self, x):
        b, c, h, w = x.shape

        x = x.reshape(b * self.groups, -1, h, w)
        x_0, x_1 = x.chunk(2, dim=1)

        # channel attention
        xn = self.avg_pool(x_0)
        xn = self.cweight * xn + self.cbias
        xn = x_0 * self.sigmoid(xn)

        # spatial attention
        xs = self.gn(x_1)
        xs = self.sweight * xs + self.sbias
        xs = x_1 * self.sigmoid(xs)

        # concatenate along channel axis
        out = torch.cat([xn, xs], dim=1)
        out = out.reshape(b, -1, h, w)

        out = self.channel_shuffle(out, 2)
        return out
```



