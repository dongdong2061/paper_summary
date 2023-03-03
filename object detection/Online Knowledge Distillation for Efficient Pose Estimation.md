# Online Knowledge Distillation for Efficient Pose Estimation

ICCV 2021  Zheng Li(杭州师范大学),Jingwen Ye(浙江大学),Mingli Song(浙江大学),Ying Huang,Zhigeng Pan

### 1.问题动机

FPD（Fast Human Pose Estimation. CVPR 19'）首先提出，利用传统的蒸馏(KD)方法，首先训一个8-stack HG作为teacher，选择一个4-stack HG作为student，然后进行KD。参考Fig. 1。

![img](E:\paper\paper_summary\image\v2-8a2ccaf739bf3a7f986d35ff12e63d05_r.jpg)

这篇工作明显存在着几点问题：

(1). 第一步训teacher，第二步训student，整体是一个two-stage的过程，较为繁琐。

(2). 如果要利用FPD训练一个8-stack HG的student，就需要找到一个比8-stack更高的model去作为teacher。堆叠更多的HG会存在性能收益递减，并且带来计算量直线上升。

(3). KD过程中同时使用Mean Squared Error(MSE)作为传统任务的监督loss和kd loss，训练时一个output同时针对两个target进行优化会带来明显的冲突。



### 2.解决思路

![image-20230303102550981](E:\paper\paper_summary\image\image-20230303102550981.png)

**(1).** 我们提出了一个在线知识蒸馏的框架，即一个多分支结构。这里的teacher不是显式存在的，而是通过多个学生分支的结果经过了FAU的ensemble形成的，即established on the fly，我们利用ensemble得到的结果（拥有更高的准确率）来扮演teacher的角色，来KD每个的学生分支，即在Fig.2 (b)中的三个小分支。

具体来说就是，如果要得到一个4-stack HG的网络，FPD的方式如(a)所示，先训练一个8-stack，然后进行KD。而在我们的方法，如图(b)，直接建立一个多分支网络（图中为3个分支），其中每个分支视为student，要得到一个4-stack HG，那么我们选择在前部share 2个stack（节约计算量），后面针对每一个branch，我们将剩下的2个stack HG独立出来，以保持diversity。三个分支产生的结果经过FAU进行ensemble，得到的ensemble heatmap再KD回每一个student分支。

我们的方法带来的直接的好处就是，整个的KD过程被简化到了**one-stage**，并且不需要手动的选择一个更高performance的teacher来进行KD。Online KD的方法直接训练完了之后，选择一个最好性能的分支，去除掉其他多余分支结构即可得到一个更高acc的目标HG网络。

那么从这里也可以直接看出我们的多分支网络更省计算量，粗略的算，FPD的方法总共会需要8+4=12个stack参与计算，我们的方法，只会有2x4=8个stack进行计算。

**(2).** 既然是一个多分支结构，那么每个分支的情况可不可以是adaptive的？既然在分支里更多的stack可以产生更好的heatmap，那么必然也就会带来ensemble结果的提升，进而KD的效果就会更好。于是针对(b)的这种每个分支都是一样的balance的结构，我们更进一步提出了unbalance结构。

具体的来说，要KD得到一个4-stack HG，即Fig.2 (c)中的第一个branch，2+2=4个stack的主分支，通过在辅助分支堆叠更多的HG来产生更好的ensemble结果，这里就是第二个分支是2+4=6个stack，第三个分支2+6=8个stack的情况。

在不考虑训练计算量的情况下，在部署时移除辅助分支，相比于balance结构，可以得到更好的main branch，即目标的4-stack HG。

**(3).** 这里的FAU，即Feature Aggregation Unit，是用来对每个分支产生的结果进行一个带有weight的channel-wise的ensemble。即将每个heatmap按照生成的权重进行集成。具体的结构如下图所示。

![image-20230303102853922](E:\paper\paper_summary\image\image-20230303102853922.png)

### 3.总结与思考

文章针对cvpr2019的FPD存在的问题进行改进，通过隐式的teacher-student模型进行konwledge distillation,这样成功地将KD简化为one-stage。另外FAU借鉴于SKnet解决图像中人物大小不统一的问题，能够同时获得局部和全局信息。

后续工作可以借鉴这个one-stage的KD方法。
