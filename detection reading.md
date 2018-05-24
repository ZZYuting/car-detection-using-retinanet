# RCNN

![52565828176](C:\Users\ADMINI~1\AppData\Local\Temp\1525658281760.png)

RCNN使用SS算法提取region proposal，将提取出的2000个左右proposal （每个wrap成固定尺寸），送入CNN提取出特征（由于对每个proposal都使用一遍CNN，所以速度慢），再在特征上加上分类器，判断feature对应的区域是属于具体某类object还是背景。RBG还用了区域对应的feature做了针对boundingbox的回归，用来修正预测的boundingbox的位置。

# Fast R-CNN

![52565829462](C:\Users\ADMINI~1\AppData\Local\Temp\1525658294621.png)

事先提取proposal,然后整张图片送入卷积层得到feature map,将proposal的位置映射到feature map从而得到每个proposal 所对应的特征，由于没有对原图的尺寸进行限制，所以又添加了ROI pooling层对feature维数归一化，再用这些特征训练几个linear SVM做判别。

# Faster-RCNN

**faster算法采用一个全卷积网络用作为region proposal网络，用该网络在feature map上滑动，并在每个位置引入多尺度多长宽比的anchor窗口来捕捉未知尺寸和ratio rate的目标；**

RPN网络的训练目标也是一个多任务的目标，同时学习分类和回归目标的包围盒；