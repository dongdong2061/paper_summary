## Research on License Plate Recognition Algorithms Based on Deep Learning in Complex Environment

（date of publication May 14, 2020）

#### 1. 作者

WANG WEIHONG1 AND TU JIAOYANG （School of Computing, Zhejiang University of Technology）



#### 2.车牌识别中存在的主要问题

1. 车牌倾斜（license plate detection）

   - ```
     License plate tilt consists of vertical tilt, horizontal tilt and both.
     ```

   - solutions:

   - ![image-20230205132422027](image\image-20230205132422027.png)

2. 图像噪音（image with noise）

   - ```
     The sources of noise in the image may come from various ways such as image acquisition, transmission and compression, and the types of noise can also be divided into pepper and salt noise, gaussian noise and so on.
     ```

   - ![image-20230205132515146](image\image-20230205132515146.png)

3. 字符模糊（fuzzy license）

   - ```
     The license plate resolution in the image is too low, resulting in poor detection effect, so a variety of methods to improve the resolution are proposed.
     ```

   

#### 3. 车牌检测的一般流程

1. 车牌检测（ LICENSE PLATE DETECTION）

   - 直接定位( DIRECT LOCATION)

   - 间接定位(INDIRECT LOCATION)

     ```
     列如：根据车牌在两个车前灯的中间部分，由此进行定位
     ```

2. 字符识别

   1. 字符分割识别

      - ```
        先将车牌字符分割逐个后进行ocr识别
        ```

   2. 字符直接识别

      - ```
        直接对定位到的车牌图片进行ocr识别
        ```




#### 4. 未来可能的研究方向

1. 评价体系的多样化和包含各种场景的测试集
2. 需要找到一个可以用统一模型进行端到端训练和测试的并且满足实际应用的系统。

