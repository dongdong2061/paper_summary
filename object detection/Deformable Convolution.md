# Deformable Convolution network

可变形卷积很好理解，但如何实现呢？实现方面需要关注两个限制：
1、如何将它变成单独的一个层，而不影响别的层；
2、在前向传播实现可变性卷积中，如何能有效地进行反向传播。
这两个问题的答案分别是：
1、在实际操作时，并不是真正地把卷积核进行扩展，而是对卷积前图片的像素重新整合，
变相地实现卷积核的扩张；
2、在图片像素整合时，需要对像素进行偏移操作，偏移量的生成会产生浮点数类型，
而偏移量又必须转换为整形，直接对偏移量取整的话无法进行反向传播，这时采用双线性差值的方式来得到对应的像素。

![image-20230320193000875](https://github.com/dongdong2061/paper_summary/blob/master/image/dcn)

![深度学习基础_202303201927_41954 1](https://github.com/dongdong2061/paper_summary/blob/master/image/dcn_note1)

![深度学习基础_202303201927_41954 2](https://github.com/dongdong2061/paper_summary/blob/master/image/dcn_note2)

可变性卷积的流程为：
1、原始图片batch（大小为b*h*w*c），记为U，经过一个普通卷积，卷积填充为same，即输出输入大小不变，
对应的输出结果为（b*h*w*2c)，记为V，输出的结果是指原图片batch中每个像素的偏移量（x偏移与y偏移，因此为2c）。
2、将U中图片的像素索引值与V相加，得到偏移后的position（即在原始图片U中的坐标值），需要将position值限定为图片大小以内。
position的大小为（b*h*w*2c)，但position只是一个坐标值，而且还是float类型的，我们需要这些float类型的坐标值获取像素。
3、例，取一个坐标值（a,b)，将其转换为四个整数，floor(a), ceil(a), floor(b), ceil(b)，将这四个整数进行整合，
得到四对坐标（floor(a),floor(b)), ((floor(a),ceil(b)), ((ceil(a),floor(b)), ((ceil(a),ceil(b))。这四对坐标每个坐标都对应U
中的一个像素值，而我们需要得到(a,b)的像素值，这里采用双线性差值的方式计算
（一方面得到的像素准确，另一方面可以进行反向传播）。
4、在得到position的所有像素后，即得到了一个新图片M，将这个新图片M作为输入数据输入到别的层中，如普通卷积。
