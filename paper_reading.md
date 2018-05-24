# RCNN

![52565828176](C:\Users\ADMINI~1\AppData\Local\Temp\1525658281760.png)

RCNN使用SS算法提取region proposal，将提取出的2000个左右proposal （每个wrap成固定尺寸），送入CNN提取出特征（由于对每个proposal都使用一遍CNN，所以速度慢），再在特征上加上分类器，判断feature对应的区域是属于具体某类object还是背景。RBG还用了区域对应的feature做了针对boundingbox的回归，用来修正预测的boundingbox的位置。

# Fast R-CNN

![52565829462](C:\Users\ADMINI~1\AppData\Local\Temp\1525658294621.png)

事先提取proposal,然后整张图片送入卷积层得到feature map,将proposal的位置映射到feature map从而得到每个proposal 所对应的特征，由于没有对原图的尺寸进行限制，所以又添加了ROI pooling层对feature维数归一化，再用这些特征训练几个linear SVM做判别

# Faster R-CNN

![52569449614](C:\Users\ADMINI~1\AppData\Local\Temp\1525694496141.png)

1. Conv layers。作为一种CNN网络目标检测方法，Faster R-CNN首先使用一组基础的conv+relu+pooling层提取image的feature maps。该feature maps被共享用于后续RPN层和全连接层。

2. Region Proposal Networks。RPN网络用于生成region proposals。该层通过softmax判断anchors属于foreground或者background，再利用bounding box regression修正anchors获得精确的proposals。

3. Roi Pooling。该层收集输入的feature maps和proposals，综合这些信息后提取proposal feature maps，送入后续全连接层判定目标类别。

4. Classification。利用proposal feature maps计算proposal的类别，同时再次bounding box regression获得检测框最终的精确位置。

5. **faster算法采用一个全卷积网络用作为region proposal网络，用该网络在feature map上滑动，并在每个位置引入多尺度多长宽比的anchor窗口来捕捉未知尺寸和ratio rate的目标；**

   RPN网络的训练目标也是一个多任务的目标，同时学习分类和回归目标的包围盒；

   # YOLO

   ![52574378433](C:\Users\ADMINI~1\AppData\Local\Temp\1525743784338.png)

   ![52574380397](C:\Users\ADMINI~1\AppData\Local\Temp\1525743803976.png)

   ​
