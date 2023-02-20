## YOLO5Face: Why Reinventing a Face Detector

#### 1.**问题动机**

人脸检测作为人脸识别、验证、跟踪、对齐、表情分析等诸多任务的第一步,因此进一步提高人脸检测的精度和实时性是有必要的.

#### 2.**解决思路：**

在本文中，作者将人脸检测看作是一般的目标检测问题，考虑到【large faces, small faces, landmark supervision, for different complexities and applications.】，提出了以YOLOv5为基础进行修改的新的模型网络YOLOv5Face.

#### 3.文章贡献

1. 在YOLOv5中添加了一个landmark regression head，用于获得所选的五个点的坐标。同时，采用了wing loss function用于计算点的误差

2. 使用了Stem block structure代替了原YOLOv5中的Focus layer，在未降低性能的前提下不仅提升了模型的泛化能力，也减少了计算消耗。

   ![image-20230209140622956](E:\paper_summary\image\stem block.png)

   

3. 将yolov5中的spp结构中的卷积核变得更小【13x13,9x9,5x5】--> 【7x7,5x5,3x3】,目的是为了更适合人脸检测

4. 增加了strde=64的P6输出模块，提高了检测large face的能力

5. **发现**：up-down flipping不能提高检测精度，Mosaic augmentation不一定总是增强检测效果，Random cropping则是对检测起到了一定帮助。

#### 4.网络结构图

![image-20230209140834987](E:\paper_summary\image\YOLOv5Face.png)

#### 5.总结与思考

本文基于YOLOv5提出了一个具有SOTA水平的人脸检测器，并且利用了关键点加以辅助进行人脸识别，具有了较好的人脸识别性能。作者借鉴了MTCNN[Joint face detection and alignment using multitask cascaded convolutional networks]的思想将用于检测人脸的68个关键点缩减为主要的5个关键点。
