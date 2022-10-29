---
date: 2022-08-19
tags: 深度学习
categories: 深度学习
cover: /images/文章图片/20220819-YOLO算法python实现/001.jpg
cover-title: YOLO算法python实现
cover-subtitle: 介绍其基本原理及实现细节
--- 

###  YOLO框架如何运作

#### YOLO用于检测给定图像中的对象的处理步骤

- 首先，输入图像：

{% image /images/文章图片/20220819-YOLO算法python实现/005.png %}

- 然后，YOLO将输入图像划分为网格形式（例如3 X 3）：

{% image /images/文章图片/20220819-YOLO算法python实现/006.png %}

- 最后，对每个网格应用图像分类和定位处理，获得预测对象的边界框及其对应的类概率。


整个过程是不是很清晰，下面逐一详细介绍。首先需要将标记数据传递给模型以进行训练。假设已将图像划分为大小为3 X 3的网格，且总共只有3个类别，分别是行人（c1）、汽车（c2）和摩托车（c3）。因此，对于每个单元格，标签y将是一个八维向量：

{% image /images/文章图片/20220819-YOLO算法python实现/007.png %}

其中：
- pc定义对象是否存在于网格中（存在的概率）；
- bx、by、bh、bw指定边界框；
- c1、c2、c3代表类别。如果检测对象是汽车，则c2位置处的值将为1，c1和c3处的值将为0；

假设从上面的例子中选择第一个网格：

{% image /images/文章图片/20220819-YOLO算法python实现/008.png %}

由于此网格中没有对象，因此pc将为零，此网格的y标签将为：

{% image /images/文章图片/20220819-YOLO算法python实现/009.png %}

？意味着其它值是什么并不重要，因为网格中没有对象。下面举例另一个有车的网格（c2=1）：

{% image /images/文章图片/20220819-YOLO算法python实现/010.png %}

在为此网格编写y标签之前，首先要了解YOLO如何确定网格中是否存在实际对象。大图中有两个物体（两辆车），因此YOLO将取这两个物体的中心点，物体将被分配到包含这些物体中心的网格中。中心点左侧网格的y标签会是这样的：

{% image /images/文章图片/20220819-YOLO算法python实现/011.png %}

由于此网格中存在对象，因此pc将等于1，bx、by、bh、bw将相对于正在处理的特定网格单元计算。由于检测出的对象是汽车，所以c2=1，c1和c3均为0。对于9个网格中的每一个单元格，都具有八维输出向量。最终的输出形状为3X3X8。

使用上面的例子（输入图像：100X100X3，输出：3X3X8），模型将按如下方式进行训练：

{% image /images/文章图片/20220819-YOLO算法python实现/012.png %}

使用经典的CNN网络构建模型，并进行模型训练。在测试阶段，将图像传递给模型，经过一次前向传播就得到输出y。为了简单起见，使用3X3网格解释这一点，但通常在实际场景中会采用更大的网格（比如19X19）。

即使一个对象跨越多个网格，它也只会被分配到其中点所在的单个网格。可以通过增加更多网格来减少多个对象出现在同一网格单元中的几率。

#### 如何编码边界框

如前所述，bx、by、bh和bw是相对于正在处理的网格单元计算而言的。下面通过一个例子来说明这一点。以包含汽车的右边网格为例：

{% image /images/文章图片/20220819-YOLO算法python实现/013.png %}

由于bx、by、bh和bw将仅相对于该网格计算。此网格的y标签将为：

{% image /images/文章图片/20220819-YOLO算法python实现/014.png %}

由于这个网格中有一个对象汽车，所以pc=1、c2=1。现在，看看如何决定bx、by、bh和bw的取值。在YOLO中，分配给所有网格的坐标都如下图所示：

{% image /images/文章图片/20220819-YOLO算法python实现/015.png %}

bx、by是对象相对于该网格的中心点的x和y坐标。在例子中，近似bx=0.4和by=0.3：

{% image /images/文章图片/20220819-YOLO算法python实现/016.png %}

bh是边界框的高度与相应单元网格的高度之比，在例子中约为0.9：bh=0.9，bw是边界框的宽度与网格单元的宽度之比，bw=0.5。此网格的y标签将为：

{% image /images/文章图片/20220819-YOLO算法python实现/017.png %}

请注意，bx和by将始终介于0和1之间，因为中心点始终位于网格内，而在边界框的尺寸大于网格尺寸的情况下，bh和bw可以大于1。

#### 非极大值抑制|Non-Max Suppression

这里有一些思考的问题——如何判断预测的边界框是否是一个好结果（或一个坏结果）？单元格之间的交叉点，计算实际边界框和预测的边界框的并集交集。假设汽车的实际和预测边界框如下所示：

{% image /images/文章图片/20220819-YOLO算法python实现/018.png %}

其中，红色框是实际的边界框，蓝色框是预测的边界框。如何判断它是否是一个好的预测呢？IoU将计算这两个框的并集交叉区域：

{% image /images/文章图片/20220819-YOLO算法python实现/019.png %}

- IoU =交叉面积/联合的面积；
- 在本例中：IoU =黄色面积/绿色面积；

如果IoU大于0.5，就可以说预测足够好。0.5是在这里采取的任意阈值，也可以根据具体问题进行更改。阈值越大，预测就越准确。

还有一种技术可以显着提高YOLO的效果——非极大值抑制。

对象检测算法最常见的问题之一是，它不是一次仅检测出一次对象，而可能获得多次检测结果。假设：

{% image /images/文章图片/20220819-YOLO算法python实现/020.png %}

上图中，汽车不止一次被识别，那么如何判定边界框呢。非极大值抑可以解决这个问题，使得每个对象只能进行一次检测。下面了解该方法的工作原理。


1. 它首先查看与每次检测相关的概率并取最大的概率。在上图中，0.9是最高概率，因此首先选择概率为0.9的方框：

{% image /images/文章图片/20220819-YOLO算法python实现/021.png %}

2. 现在，它会查看图像中的所有其他框。与当前边界框较高的IoU的边界框将被抑制。因此，在示例中，0.6和0.7概率的边界框将被抑制：

{% image /images/文章图片/20220819-YOLO算法python实现/022.png %}

3. 在部分边界框被抑制后，它会从概率最高的所有边界框中选择下一个，在例子中为0.8的边界框：

{% image /images/文章图片/20220819-YOLO算法python实现/023.png %}

4. 再次计算与该边界框相连边界框的IoU，去掉较高IoU值的边界框：

{% image /images/文章图片/20220819-YOLO算法python实现/024.png %}

5. 重复这些步骤，得到最后的边界框：

{% image /images/文章图片/20220819-YOLO算法python实现/025.png %}

以上就是非极大值抑制的全部内容，总结一下关于非极大值抑制算法的要点：

- 丢弃概率小于或等于预定阈值（例如0.5）的所有方框；
- 对于剩余的边界框：
- 选择具有最高概率的边界框并将其作为输出预测；
- 计算相关联的边界框的IoU值，舍去IoU大于阈值的边界框；
- 重复步骤2，直到所有边界框都被视为输出预测或被舍弃；

#### Anchor Boxes

在上述内容中，每个网格只能识别一个对象。但是如果单个网格中有多个对象呢？这就行需要了解 Anchor Boxes的概念。假设将下图按照3X3网格划分：

{% image /images/文章图片/20220819-YOLO算法python实现/026.png %}

获取对象的中心点，并根据其位置将对象分配给相应的网格。在上面的示例中，两个对象的中心点位于同一网格中：

{% image /images/文章图片/20220819-YOLO算法python实现/027.png %}

上述方法只会获得两个边界框其中的一个，但是如果使用Anchor Boxes，可能会输出两个边界框！我们该怎么做呢？首先，预先定义两种不同的形状，称为Anchor Boxes。对于每个网格将有两个输出。这里为了易于理解，这里选取两个Anchor Boxes，也可以根据实际情况增加Anchor Boxes的数量：

{% image /images/文章图片/20220819-YOLO算法python实现/028.png %}

- 没有Anchor Boxes的YOLO输出标签如下所示：

{% image /images/文章图片/20220819-YOLO算法python实现/029.png %}

- 有Anchor Boxes的YOLO输出标签如下所示：

{% image /images/文章图片/20220819-YOLO算法python实现/030.png %}

前8行属于Anchor Boxes1，其余8行属于Anchor Boxes2。基于边界框和框形状的相似性将对象分配给Anchor Boxes。由于Anchor Boxes1的形状类似于人的边界框，后者将被分配给Anchor Boxes1，并且车将被分配给Anchor Boxes2.在这种情况下的输出，将是3X3X16大小。

 因此，对于每个网格，可以根据Anchor Boxes的数量检测两个或更多个对象。


### 结合思想

首先介绍如何训练YOLO模型，然后是新的图像进行预测。

#### 训练

训练模型时，输入数据是由图像及其相应的y标签构成。样例如下：

{% image /images/文章图片/20220819-YOLO算法python实现/031.png %}

假设每个网格有两个Anchor Boxes，并划分为3X3网格，并且有3个不同的类别。因此，相应的y标签具有3X3X16的形状。训练过程的完成方式就是将特定形状的图像映射到对应3X3X16大小的目标。

#### 测试

对于每个网格，模型将预测·3X3X16·大小的输出。该预测中的16个值将与训练标签的格式相同。前8个值将对应于Anchor Boxes1，其中第一个值将是该网络中对象的概率，2-5的值将是该对象的边界框坐标，最后三个值表明对象属于哪个类。以此类推。

最后，非极大值抑制方法将应用于预测框以获得每个对象的单个预测结果。

以下是YOLO算法遵循的确切维度和步骤：

- 准备对应的图像（608,608,3）;
- 将图像传递给卷积神经网络（CNN），该网络返回（19,19,5,85）维输出;
- 输出的最后两个维度被展平以获得（19,19,425）的输出量：
	- 19×19网格的每个单元返回425个数字;
	- 425=5 * 85，其中5是每个网格的Anchor Boxes数量；
	- 85= 5+80，其中5表示（pc、bx、by、bh、bw），80是检测的类别数；
- 最后，使用IoU和非极大值抑制去除重叠框；


### YOLO算法实现

首先定义一些函数，这些函数将用来选择高于某个阈值的边界框，并对其应用非极大值抑制。首先，导入所需的库：

```
import os
import matplotlib.pyplot as plt
from matplotlib.pyplot import imshow
import scipy.io
import scipy.misc
import numpy as np
import pandas as pd
import PIL
import tensorflow as tf
from skimage.transform import resize
from keras import backend as K
from keras.layers import Input, Lambda, Conv2D
from keras.models import load_model, Model
from yolo_utils import read_classes, read_anchors, generate_colors, preprocess_image, draw_boxes, scale_boxes
from yad2k.models.keras_yolo import yolo_head, yolo_boxes_to_corners, preprocess_true_boxes, yolo_loss, yolo_body

%matplotlib inline
```

然后，实现基于概率和阈值过滤边界框的函数：

```
def yolo_filter_boxes(box_confidence, boxes, box_class_probs, threshold = .6):
    box_scores = box_confidence*box_class_probs
    box_classes = K.argmax(box_scores,-1)
    box_class_scores = K.max(box_scores,-1)
    filtering_mask = box_class_scores>threshold
    scores = tf.boolean_mask(box_class_scores,filtering_mask)
    boxes = tf.boolean_mask(boxes,filtering_mask)
    classes = tf.boolean_mask(box_classes,filtering_mask)

    return scores, boxes, classes
```

之后，实现计算IoU的函数：

```
def iou(box1, box2):
    xi1 = max(box1[0],box2[0])
    yi1 = max(box1[1],box2[1])
    xi2 = min(box1[2],box2[2])
    yi2 = min(box1[3],box2[3])
    inter_area = (yi2-yi1)*(xi2-xi1)
    box1_area = (box1[3]-box1[1])*(box1[2]-box1[0])
    box2_area = (box2[3]-box2[1])*(box2[2]-box2[0])
    union_area = box1_area+box2_area-inter_area
    iou = inter_area/union_area

    return iou
```

然后，实现非极大值抑制的函数：

```
def yolo_non_max_suppression(scores, boxes, classes, max_boxes = 10, iou_threshold = 0.5):
    max_boxes_tensor = K.variable(max_boxes, dtype='int32')
    K.get_session().run(tf.variables_initializer([max_boxes_tensor]))
    nms_indices = tf.image.non_max_suppression(boxes,scores,max_boxes,iou_threshold)
    scores = K.gather(scores,nms_indices)
    boxes = K.gather(boxes,nms_indices)
    classes = K.gather(classes,nms_indices)

    return scores, boxes, classes
```

随机初始化下大小为（19,19,5,85）的输出向量：

```
yolo_outputs = (tf.random_normal([19, 19, 5, 1], mean=1, stddev=4, seed = 1),
                   tf.random_normal([19, 19, 5, 2], mean=1, stddev=4, seed = 1),
                   tf.random_normal([19, 19, 5, 2], mean=1, stddev=4, seed = 1),
                   tf.random_normal([19, 19, 5, 80], mean=1, stddev=4, seed = 1))
```

最后，实现一个将CNN的输出作为输入并返回被抑制的边界框的函数：
```
def yolo_eval(yolo_outputs, image_shape = (720., 1280.), max_boxes=10, score_threshold=.6, iou_threshold=.5):
    box_confidence, box_xy, box_wh, box_class_probs = yolo_outputs
    boxes = yolo_boxes_to_corners(box_xy, box_wh)
    scores, boxes, classes = yolo_filter_boxes(box_confidence, boxes, box_class_probs, threshold = score_threshold)
    boxes = scale_boxes(boxes, image_shape)
    scores, boxes, classes = yolo_non_max_suppression(scores, boxes, classes, max_boxes, iou_threshold)

    return scores, boxes, classes
```

使用yolo_eval函数对之前创建的随机输出向量进行预测：
```
scores, boxes, classes = yolo_eval(yolo_outputs)
with tf.Session() as test_b:
    print("scores[2] = " + str(scores[2].eval()))
    print("boxes[2] = " + str(boxes[2].eval()))
    print("classes[2] = " + str(classes[2].eval()))
```

{% image /images/文章图片/20220819-YOLO算法python实现/032.png %}

score表示对象在图像中的可能性，boxes返回检测到的对象的（x1，y1，x2，y2）坐标，classes表示识别对象所属的类。
现在，在新的图像上使用预训练的YOLO算法，看看其工作效果：

```
sess = K.get_session()
class_names = read_classes("model_data/coco_classes.txt")
anchors = read_anchors("model_data/yolo_anchors.txt")

yolo_model = load_model("model_data/yolo.h5")
```

在加载类别信息和预训练模型之后，使用上面定义的函数来获取 yolo_outputs
```
yolo_outputs = yolo_head(yolo_model.output, anchors, len(class_names))
```

之后，定义一个函数来预测边界框并在图像上标记边界框：

```
def predict(sess, image_file):
    image, image_data = preprocess_image("images/" + image_file, model_image_size = (608, 608))
    out_scores, out_boxes, out_classes = sess.run([scores, boxes, classes], feed_dict={yolo_model.input: image_data, K.learning_phase(): 0})

    print('Found {} boxes for {}'.format(len(out_boxes), image_file))

    # Generate colors for drawing bounding boxes.
    colors = generate_colors(class_names)

    # Draw bounding boxes on the image file
    draw_boxes(image, out_scores, out_boxes, out_classes, class_names, colors)

    # Save the predicted bounding box on the image
    image.save(os.path.join("out", image_file), quality=90)

    # Display the results in the notebook
    output_image = scipy.misc.imread(os.path.join("out", image_file))

    plt.figure(figsize=(12,12))
    imshow(output_image)

    return out_scores, out_boxes, out_classes
```

接下来，将使用预测函数读取图像并进行预测：

```
img = plt.imread('images/img.jpg')
image_shape = float(img.shape[0]), float(img.shape[1])
scores, boxes, classes = yolo_eval(yolo_outputs, image_shape)
```

最后，输出预测结果：

```
out_scores, out_boxes, out_classes = predict(sess, "img.jpg")
```

{% image /images/文章图片/20220819-YOLO算法python实现/033.png %}


#### YOLO算法darknet的官网

https://pjreddie.com/darknet/yolo/




