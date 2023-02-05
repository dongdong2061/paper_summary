# Object detection



## ReDet: A Rotation-equivariant Detector for Aerial Object Detection

（CPVPR2021）代码：[csuhan/ReDet: Official code of the paper "ReDet: A Rotation-Equivariant Detector for Aerial Object Detection" (CVPR 2021) (github.com)](https://github.com/csuhan/ReDet)

![image-20230115093121836](E:\paper_summary\image\image-20230115093121836.png)

#### 1.解决的问题

- CNN不具有旋转等变性——指输入图像旋转后，CNN提取到的特征发生了变化

  **解决方案**：利用NIPS2019的e2cnn思想重写了ResNet50并命名为ReCNN，使得CNN具有旋转等变性。即当输入图像发生旋转时，CNN提取到的特征一样。

- RRoIAlign(Rotated RoI)仅仅是空间维度对齐，并没有在通道维度上对齐

  **解决方案**:在经过e2cnn提取到图像的特征向量F(K*N,H,W)后，在通道维度上，可以理解为划分成N个组(N=4/8)代表4个方向或8个方向，而每组的子通道数为K。但RRoIAlign( Rotation-invariant RoI Align)模块仅仅是对于不同朝向的物体在空间维度上进行了校正，但在通道维度上并不对齐,故作者设计了RiROIAlign模块在通道维度上和空间维度上均进行了对齐，从而得到了旋转不变性的特征。

​		

#### 2.模型结构

![image-20230115102850412](E:\paper_summary\image\image-20230115102850412.png)

##### 整体流程（两阶段）：

在ReCNN提取到旋转等变特征图（K,N,H,W）后，之后RPN生成旋转感兴趣区域，经过RiRoI Align模块生成旋转不变特征，之后将其进行分类和回归。

#### ReCNN

这里作者利用e2cnn的思想实现了ReCNN，在[ImageNet](https://so.csdn.net/so/search?q=ImageNet&spm=1001.2101.3001.7020)上重新训练并在测试数据集上微调。

#### RoI transformer

[Learning RoI Transformer for Oriented Object Detection in Aerial Images (thecvf.com)](https://openaccess.thecvf.com/content_CVPR_2019/papers/Ding_Learning_RoI_Transformer_for_Oriented_Object_Detection_in_Aerial_Images_CVPR_2019_paper.pdf)

#### **RiRoiAlign**

![img](E:\paper_summary\image\watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5q2m5LmQ5LmQfg==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

假设N=4，即划分成4组通道，对应代码中的nOrientation。r即ind，o代表池化后特征向量的通道下标。以表格为例，当r=1时，o=1时候，即将输入通道的0和1号通道像素值拿去做RiRoiAlign，并将计算得到像素值放到o=1号位置，即实现了通道对齐。

####  Conclusions

本文提出了一个两阶段的旋转目标检测器，第一阶段首先利用ReCNN提取出具有旋转等变性的feature maps，然后利用RiRoi Align(Rotation-invariant RoI Align)获得旋转不变性的feature maps。