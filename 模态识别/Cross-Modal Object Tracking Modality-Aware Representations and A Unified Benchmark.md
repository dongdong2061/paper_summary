#### Cross-Modal Object Tracking: Modality-Aware Representations and A Unified Benchmark

Chenglong Li1, Tianhao Zhu, Lei Liu, Xiaonan Si , Zilin Fan , Sulan Zhai

文章首先提出了跨模态目标跟踪新任务，旨在解决可见光与红外等模态的成像差异问题，以及一个可即插即用的模块[a Modality-Aware cross-Modal Object Tracking algorithm (MArMOT)]，模块的结构示意图如下：

![image-20230220151700351]([E:\paper_summary\image\image-MarMOT.png](https://github.com/dongdong2061/paper_summary/blob/master/image/image-MarMOT.png))

Modality-AWare Branch是借鉴于Inception网络，（可以将标准的 d × d 卷积分解为1×d 和 d × 1 卷积，以减少参数量）

在跟踪过程中，我们在每一帧中只有一个模态作为输入，不知道呈现的是哪种模态。为了解决这个问题，我们设计了一个集成层（ensemble layer），在给定一个模态输入的情况下，自适应地集成两个分支输出的特征。通过这种方法，无论输入哪种模态，我们都可以得到有效的特征。
