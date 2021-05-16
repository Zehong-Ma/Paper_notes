# Yolo

## 1. Abstract

+ 之前的目标检测是将分类器修改后进行检测，是一个分类任务；而Yolo是一个对bounding box和对应class的**回归任务**。Yolo是一个单一的端到端的网络，可以在一次评估中预测出bounding box和对应class。
+ Yolo实时性很好，预测出背景中假正例的概率更低，但是bounding box定位误差大。

> 误检率： fp rate = sum(fp) / (sum(fp) + sum(tn))
>
> 查准率： precision rate = sum(tp) /  (sum(tp) + sum(fp)) 
>
> 查全率： recall rate = sum(tp) / (sum(tp) + sum(fn))
>
> 常用的还有一个漏检率：miss rate = sum(fn) / (sum(tp) + sum(fn))
>
> recall rate + miss rate  = 1

## 2. introduction

### 2.1 基于classifiers的目标检测

​	使用一个分类器在一张图片的不同位置，按不同的缩放比例裁取图片进行分类。

+ DPM使用滑动窗口均匀地扫过整张图片并对每个窗口进行分类。
+ RCNN首先利用region proposal method（区域建议方法）生成可能的bounding box，然后在每个可能的bounding box上利用分类器进行分类，分类之后，post-processing用来修正bbox，排除重复的检测，然后考虑场景中其他的检出物体，并一起重新计算score。这种方法比较慢并且不容易优化，因为每个物体都要单独使用一个分类器训练。

### 2.2 Yolo（you only look once）

+ **回归任务**：Yolo则是利用图片中的像素值直接回归出bounding box的坐标和对应的class probability。这样只需要查看图片一次便可以知道图片中有哪些物体（what），并且这些物体分别在哪里（where）。

+ **全局信息**：yolo是通过全图进行推理的，所以它可以编码隐含的上下文信息。YOLO提供更大的上下文信息很好的克服了Fast-RCNN将背景中细小区域检出的问题，所以YOLO假正例的数目少于Fast R-CNN假正例的一半。
+ **物体的一般化表示**：YOLO学习到了物体的一般化表示。YOLO由于其高度可归纳化，它对自然图片或者新的，未知的的图片都有很强的兼容性，一般很少出问题。

## 3.Unified Detection

+ 把图片分成S*S个网格（如果物体的中心落在某个网格，那个这个网格就用来检测该物体）
+ 置信率

$$
Pr(Object)*IOU^{truth}_{pred}
$$

​		Pr(Object)表示每个网格中有物体的概率，IOU表示ground truth和预测得到的bbox的交并比，用来表示预测得到的框的准确性

+ 回归得到的变量的维度
  $$
  S*S*(B*5+C)
  其中5表示x,y,w,h,confidence
  C代表每个框对应C个类别的概率
  $$
  

