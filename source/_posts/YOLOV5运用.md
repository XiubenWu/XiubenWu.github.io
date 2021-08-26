---
title: YOLOV5运用
photos:
  - https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20210826YOLOv5.png
excerpt: ----
date: 2021-08-26 22:59:19
tags:
  -	DL
categories:
  -	笔记
---



# 源码下载

[ultralytics团队贡献源码](https://github.com/ultralytics/yolov5)，以及官方[帮助文档](https://docs.ultralytics.com/).

- 下载

```
$ git clone https://github.com/ultralytics/yolov5.git
```

- 依赖安装

```
$ pip install -r requirements.txt
```



***



# 检测方式

提供从Pytorch Hub进行检测和本地py文件检测方式。

- Pytorch Hub检测：

```python
import torch

# Model
model = torch.hub.load('ultralytics/yolov5', 'yolov5s')  # or yolov5m, yolov5l, yolov5x, custom

# Images
img = 'https://ultralytics.com/images/zidane.jpg'  # or file, Path, PIL, OpenCV, numpy, list

# Inference
results = model(img)

# Results
results.print()  # or .show(), .save(), .crop(), .pandas(), etc.
```

- 本地检测：

```
$ python detect.py --source 0  # webcam
                            file.jpg  # image 
                            file.mp4  # video
                            path/  # directory
                            path/*.jpg  # glob
                            'https://youtu.be/NUsoVlDFqZg'  # YouTube
                            'rtsp://example.com/media.mp4'  # RTSP, RTMP, HTTP stream
```

注意调节命令行**argparse**参数。



***



# 目录结构

- data/:主要作用为存放各类数据集中的yaml配置文件，**coco128.yaml**示例如下，主要配置其中的path(数据集根目录)、train/val/test(path下的训练集、验证集、测试集，测试集可省略)。根据自己的需要定制nc(目标种类数量)以及names(物体名称)。

```yaml
# YOLOv5 🚀 by Ultralytics, GPL-3.0 license
# COCO128 dataset https://www.kaggle.com/ultralytics/coco128 (first 128 images from COCO train2017)
# Example usage: python train.py --data coco128.yaml
# parent
# ├── yolov5
# └── datasets
#     └── coco128  ← downloads here


# Train/val/test sets as 1) dir: path/to/imgs, 2) file: path/to/imgs.txt, or 3) list: [path/to/imgs1, path/to/imgs2, ..]
path: ./datasets/coco128  # dataset root dir
train: images/train2017  # train images (relative to 'path') 128 images
val: images/train2017  # val images (relative to 'path') 128 images
test:  # test images (optional)

# Classes
nc: 80  # number of classes
names: ['person', 'bicycle', 'car', 'motorcycle', 'airplane', 'bus', 'train', 'truck', 'boat', 'traffic light',
        'fire hydrant', 'stop sign', 'parking meter', 'bench', 'bird', 'cat', 'dog', 'horse', 'sheep', 'cow',
        'elephant', 'bear', 'zebra', 'giraffe', 'backpack', 'umbrella', 'handbag', 'tie', 'suitcase', 'frisbee',
        'skis', 'snowboard', 'sports ball', 'kite', 'baseball bat', 'baseball glove', 'skateboard', 'surfboard',
        'tennis racket', 'bottle', 'wine glass', 'cup', 'fork', 'knife', 'spoon', 'bowl', 'banana', 'apple',
        'sandwich', 'orange', 'broccoli', 'carrot', 'hot dog', 'pizza', 'donut', 'cake', 'chair', 'couch',
        'potted plant', 'bed', 'dining table', 'toilet', 'tv', 'laptop', 'mouse', 'remote', 'keyboard', 'cell phone',
        'microwave', 'oven', 'toaster', 'sink', 'refrigerator', 'book', 'clock', 'vase', 'scissors', 'teddy bear',
        'hair drier', 'toothbrush']  # class names


# Download script/URL (optional)
download: https://github.com/ultralytics/yolov5/releases/download/v1.0/coco128.zip
```

- models/:文件夹中主要存放模型结构配置文件**yolov5s.yaml**如下所示，主要更改其中的nc参数。

```yaml
# YOLOv5 🚀 by Ultralytics, GPL-3.0 license

# Parameters
nc: 2  # number of classes
depth_multiple: 0.33  # model depth multiple
width_multiple: 0.50  # layer channel multiple
anchors:
  - [10,13, 16,30, 33,23]  # P3/8
  - [30,61, 62,45, 59,119]  # P4/16
  - [116,90, 156,198, 373,326]  # P5/32

# YOLOv5 backbone
backbone:
  # [from, number, module, args]
  [[-1, 1, Focus, [64, 3]],  # 0-P1/2
   [-1, 1, Conv, [128, 3, 2]],  # 1-P2/4
   [-1, 3, C3, [128]],
   [-1, 1, Conv, [256, 3, 2]],  # 3-P3/8
   [-1, 9, C3, [256]],
   [-1, 1, Conv, [512, 3, 2]],  # 5-P4/16
   [-1, 9, C3, [512]],
   [-1, 1, Conv, [1024, 3, 2]],  # 7-P5/32
   [-1, 1, SPP, [1024, [5, 9, 13]]],
   [-1, 3, C3, [1024, False]],  # 9
  ]

# YOLOv5 head
head:
  [[-1, 1, Conv, [512, 1, 1]],
   [-1, 1, nn.Upsample, [None, 2, 'nearest']],
   [[-1, 6], 1, Concat, [1]],  # cat backbone P4
   [-1, 3, C3, [512, False]],  # 13

   [-1, 1, Conv, [256, 1, 1]],
   [-1, 1, nn.Upsample, [None, 2, 'nearest']],
   [[-1, 4], 1, Concat, [1]],  # cat backbone P3
   [-1, 3, C3, [256, False]],  # 17 (P3/8-small)

   [-1, 1, Conv, [256, 3, 2]],
   [[-1, 14], 1, Concat, [1]],  # cat head P4
   [-1, 3, C3, [512, False]],  # 20 (P4/16-medium)

   [-1, 1, Conv, [512, 3, 2]],
   [[-1, 10], 1, Concat, [1]],  # cat head P5
   [-1, 3, C3, [1024, False]],  # 23 (P5/32-large)

   [[17, 20, 23], 1, Detect, [nc, anchors]],  # Detect(P3, P4, P5)
  ]

```

- utils/：主要的函数源码

## 其他

运行train.py或者detect.py后将会生成runs文件夹，其中存放训练的权重、过程参数以及检测的结果。以训练次数递增。



***



# 自建数据集

数据集结构：

-  myDatasets
  - annotations
  - images
  - labels

annotations中存放源图像的xml标注格式。使用如下脚本将xml格式转换为txt格式，并将结果存放在labels文件夹中:

```python
"""
xml2txt.py
"""
import xml.etree.ElementTree as ET
import os

classes = ["pipe", "bad"]


def convert(size, box):
    dw = 1. / (size[0])
    dh = 1. / (size[1])
    x = (box[0] + box[1]) / 2.0 - 1
    y = (box[2] + box[3]) / 2.0 - 1
    w = box[1] - box[0]
    h = box[3] - box[2]
    x = x * dw
    w = w * dw
    y = y * dh
    h = h * dh
    return x, y, w, h


def convert_annotation(rootpath, xmlname):
    xmlpath = rootpath + 'annotation/'
    xmlfile = os.path.join(xmlpath, xmlname)
    with open(xmlfile, "r") as in_file:
        txtname = xmlname[:-4] + '.txt'
        txtpath = rootpath + 'labels/'
        if not os.path.exists(txtpath):
            os.makedirs(txtpath)
        txtfile = os.path.join(txtpath, txtname)
        with open(txtfile, "w+") as out_file:
            tree = ET.parse(in_file)
            root = tree.getroot()
            size = root.find('size')
            w = int(size.find('width').text)
            h = int(size.find('height').text)
            out_file.truncate()
            for obj in root.iter('object'):
                difficult = obj.find('difficult').text
                cls = obj.find('name').text
                if cls not in classes or int(difficult) == 1:
                    continue
                cls_id = classes.index(cls)
                xmlbox = obj.find('bndbox')
                b = (float(xmlbox.find('xmin').text), float(xmlbox.find('xmax').text), float(xmlbox.find('ymin').text),
                     float(xmlbox.find('ymax').text))
                bb = convert((w, h), b)
                out_file.write(str(cls_id) + " " + " ".join([str(a) for a in bb]) + '\n')


if __name__ == "__main__":
    rootpath = './datasets/pipes/'  # 根目录
    xmlpath = rootpath + 'annotation/'
    list = os.listdir(xmlpath)
    for i in range(len(list)):
        path = os.path.join(xmlpath, list[i])
        if ('.xml' in path) or ('.XML' in path):
            convert_annotation(rootpath, list[i])
            print('done', i)
        else:
            print('not xml file', i)
```



***



# 训练模型:

根据需要自定义参数：

```
python train.py --img 640 --batch 16 --epochs 100 --data ./data/coco128.yaml --cfg ./models/yolov5s.yaml --weights yolov5s.pt
```



