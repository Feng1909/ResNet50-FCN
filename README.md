# ResNet50-FCN
论文地址：https://arxiv.org/abs/1411.4038  
运行地址：https://aistudio.baidu.com/aistudio/projectdetail/4207287?contributionType=1
## FCN：Fully Convolutional Networks
**全卷积模型**：本项目将**CNN**模式后面的**全连接层换成卷积层**，所以整个网络都是**卷积层**。其最后输出的是一张已经标记好的**热图**，而**不是一个概率值**。
通常的CNN网络中，在最后都会有几层全连接网络来融合特征信息，然后再对融合后的特征信息进行softmax分类，如下图所示：

![](https://ai-studio-static-online.cdn.bcebos.com/42fb5c97471d42ed9cb20e72288b27cc618bdd4c590b4ae2a6e0f6261d937a30)

假设最后一层的**feature_map**的大小是**7x7x512**，那么**全连接层**做的事就是用**4096个7x7x512**的滤波器去卷积这个最后的**feature_map**。所以可想而知这个参数量是很大的！！ 

![](https://ai-studio-static-online.cdn.bcebos.com/27271485ad074dd0bffb778c4b3a2fd5cdddd59351ae4812bb573b64bf7a724d)

但是**全卷积网络**就简单多了。**FCN**的做法是将最后的全连接层替换为**4096个1x1x512**的**卷积核**，所以最后得出来的就是一个二维的图像，然后再对这个二维图像进行**上采样**（反卷积），然后再对最后反卷积的图像的每个像素点进行softmax分类。  
我们都知道卷积层后的**全连接目的**是将
卷积输出的二维特征图（feature map）转化成（$N\times 1$）**一维的一个向量**因为传统的卷积神经网络的输出都是分类（一般都是**一个概率值**），也就是几个类别的概率甚至就是一个类别号，那么全连接层就是高度提纯的特征了，方便交给最后的分类器或者回归。  
根据全连接的目的，我们完全可以利用卷积层代替全连接层，在输入端使用 $M\times M$ 大小的卷积核将数据“扁平化处理”，在使用 $1\times 1$ 卷积核对数据进行降维操作，最终卷积核的通道数即是我们预测数据的维度。这样在输入端不将数据进行扁平化处理，还可以使得图片保留其空间信息:

![](https://ai-studio-static-online.cdn.bcebos.com/4834d2c5d12545e7af995ec73fb23ddb11a3d34267ac4cc3baf1d2b1969f2313)

## 使用全卷积层的优点:

* 全卷积层能够兼容不同大小的尺寸输入。
* 与global avg pooling类似，可以大大减少网络参数量

## 数据集介绍：Cifar10

链接：http://www.cs.toronto.edu/~kriz/cifar.html

![](https://ai-studio-static-online.cdn.bcebos.com/15a8790a113d41418d6fc8563aeb4acd10da73b4b8c6488599fa9e7a01cc0833)


 **CIFAR-10**是一个更接近**普适物体**的彩色图像数据集。CIFAR-10 是由Hinton 的学生Alex Krizhevsky 和Ilya Sutskever 整理的一个用于识别普适物体的小型数据集。一共包含**10 个类别**的**RGB**彩色图片：**飞机**(airplane)、**汽车**(automobile)、**鸟类**(bird)、**猫**(cat)、**鹿**(deer)、**狗**(dog)、**蛙类**(frog)、**马**(horse)、**船**(ship)和**卡车**(truck).  
   
 每个图片的尺寸为$32\times 32$，每个类别有**6000**个图像，数据集中一共有**50000**张训练图片和**10000**张测试图片。