# MobileNets模型结构
# 深度可分解卷积
# MobileNets总共28层（1 + 2 × 13 + 1 = 28）
[参考理解](https://blog.csdn.net/wfei101/article/details/78310226)

[参考代码](https://github.com/Zehaos/MobileNet/blob/master/nets/mobilenet.py)

[V1 论文地址](https://arxiv.org/pdf/1704.04861.pdf)

[MobileNet V2 论文地址](https://arxiv.org/pdf/1801.04381.pdf)

[有tensorflow的实现 v1](https://github.com/tensorflow/models/blob/master/slim/nets/mobilenet_v1.md)

[caffe也有人实现 v1](https://github.com/shicai/MobileNet-Caffe)

[MobileNetV2-pytorch](https://github.com/Randl/MobileNetV2-pytorch)


      是Google针对手机等嵌入式设备(  移动和嵌入式视觉应用 mobile and embedded vision applications)
      提出的一种轻量级的深层神经网络，取名为MobileNets。
      个人感觉论文所做工作偏向于模型压缩方面，
      核心思想就是卷积核的巧妙分解，可以有效减少网络参数。

       MobileNets是基于一个流线型的架构 streamlined architecture 
       轻量级的深层神经网络 light  weight  deep neural  networks.
       一些嵌入式平台上的应用比如机器人和自动驾驶，它们的硬件资源有限，
       就十分需要一种轻量级、低延迟（同时精度尚可接受）的网络模型

       在建立小型和有效的神经网络上，已经有了一些工作，比如SqueezeNet，Google Inception，Flattened network等等。
       大概分为压缩预训练模型和直接训练小型网络两种。
       MobileNets主要关注优化延迟，同时兼顾模型大小，不像有些模型虽然参数少，但是也慢的可以。
 
##  因此，在小型化方面常用的手段有：
      （1）卷积核分解，使用1×N和N×1的卷积核代替N×N的卷积核
      （2）使用bottleneck结构，以SqueezeNet为代表
      （3）以低精度浮点数保存，例如Deep Compression
      （4）冗余卷积核剪枝及哈弗曼编码


      将标准卷积分解成一个深度卷积和一个点卷积（1 × 1卷积核）。深度卷积将每个卷积核应用到每一个通道，
      而1 × 1卷积用来组合通道卷积的输出。后文证明，这种分解可以有效减少计算量，降低模型大小
### 模型简化思想
      3 × 3 × 3 ×16 3*3的卷积 3通道输入  16通道输出
       ===== 3 × 3 × 1 × 3的深度卷积(3个3*3的卷积核，每一个卷积核对输入通道分别卷积后叠加输出) 输出3通道   1d卷积
       ===== + 1 × 1 × 3 ×16的1 ×1点卷积 1*1卷积核 3通道输入  16通道输出
      参数数量 75/432 = 0.17

      3*3*输入通道*输出通道 -> BN -> RELU
      =======>
      3*3*1*输入通道 -> BN -> RELU ->    1*1*输入通道*输出通道 -> BN -> RELU


### A 模型的第一个超参数，即宽度乘数：
        为了构建更小和更少计算量的网络，作者引入了宽度乘数  ，
        作用是改变输入输出通道数，减少特征图数量，让网络变瘦。
###   B 第二个超参数是分辨率乘数  ，
        分辨率乘数用来改变输入数据层的分辨率，同样也能减少参数。