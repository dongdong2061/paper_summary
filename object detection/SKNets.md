## Selective Kernel Network

SKNet和SENet均是一个团队里提出来的，SENet是对通道执行[注意力机制](https://so.csdn.net/so/search?q=注意力机制&spm=1001.2101.3001.7020)，而SKNet则是对卷积核执行注意力机制，即让网络自己选择合适的卷积核。

SKNet的核心模块如下图所示：

![image-20230222114819530](https://github.com/dongdong2061/paper_summary/blob/master/image/image-SKNet.png)

在该模块中，作者使用了**多分支卷积网络**、**组卷积**、**空洞卷积**以及**注意力机制**。

Split操作是将原feature map分别通过一个3×3的分组/深度卷积和3×3的空洞卷积（感受野为5×5）生成两个feature map :U1（图中黄色）和U2（图中绿色）。然后将这两个feature map进行相加，生成U。生成的U通过Fgp函数（全局平均池化）生成1×1×C的feature map(图中的s)，该feature map通过fc函数（全连接层）生成d×1的向量(图中的z)，公式如图中所示(δ表示ReLU激活函数，B表示Batch Noramlization,W是一个d×C的维的)。d的取值是由公式d = max(C/r,L)确定，r是一个缩小的比率(与SENet中相似)，L表示d的最小值，实验中L的值为32。生成的z通过ac和bc两个函数，并将生成的函数值与原先的U1和U2相乘。由于ac和bc的函数值相加等于1,因此能够实现对分支中的feature map设置权重，因为不同的分支卷积核尺寸不同，因此实现了让网络自己选择合适的卷积核(ac和bc中的A、B矩阵均是需要在训练之前初始化的，其尺寸均为C×d)


##### 组卷积

组卷积相比于标准卷积，减少了一些参数量。假设feature map的尺寸大小为W×H×C1，卷积核的尺寸为w×h×C1，生成的feature map的尺寸大小为W×H×C2，那么标准卷积的参数量为w×h×C1×C2；如果换为分组卷积，假设分为g组，原feature map和生成的feature map尺寸同上，，那么每组卷积的参数量为w×h×(C1/g)×(C2/g)，共有g组，那么总参数量为w×h×C1×C2/g，参数量与标准卷积相比，减少为原来的1/g。

##### 空洞卷积

空洞卷积与标准卷积相比，增大了感受野。一般情况下，卷积之后的池化操作缩小feature map的尺寸也能达到增加感受野的效果，但是池化过程会导致信息的丢失，所以引入了空洞卷积操作。下图为Dilation=2时的卷积效果图，当Dilation=2时，3×3的卷积核的感受野为5×5。空洞卷积与标准卷积相比，在不增加参数量的同时增大了感受野。
![img](https://img-blog.csdnimg.cn/20191004150612218.png)

### 注意力机制

下图所示为SENet中的注意力机制模块。

![img](https://upload-images.jianshu.io/upload_images/6449203-66c690882e15b340.jpg?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

该通道注意力机制模块很好理解，上图中的Ftr函数表示标准卷积，Fsq函数表示一个全局平均池化，通过该函数，生成1×1×C的feature map。然后将该feature map送入Fex函数（该函数由两个全连接层组成），输出1×1×C的feature map。将该feature map通过sigmoid函数将值控制到（0，1）之间，然后与最开始的H×W×C的feature map对应相乘，即实现了对各个通道的权重控制，实现了注意力机制。
