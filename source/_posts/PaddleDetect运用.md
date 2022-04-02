---
title: PaddleDetect运用
photos:
  -	https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20210826Paddle.png
excerpt: ----
date: 2021-08-26 18:36:55
tags:
  -	DL
categories:
  -	笔记
---



# 简介

PaddleDetection模块化地实现了多种主流目标检测算法，提供了丰富的数据增强策略、网络模块组件（如骨干网络）、损失函数等，并集成了模型压缩和跨平台高性能部署能力。

## 环境要求

- PaddlePaddle 2.1
- OS 64位操作系统
- Python 3(3.5.1+/3.6/3.7/3.8/3.9)，64位版本
- pip/pip3(9.0.1+)，64位版本
- CUDA >= 10.1
- cuDNN >= 7.6



***



# 安装

## [安装PaddlePaddle](https://www.paddlepaddle.org.cn/install/quick?docurl=/documentation/docs/zh/install/pip/windows-pip.html)

```
# CUDA10.1
python -m pip install paddlepaddle-gpu==2.1.0.post101 -f https://paddlepaddle.org.cn/whl/mkl/stable.html

# CPU
python -m pip install paddlepaddle -i https://mirror.baidu.com/pypi/simple
```

## 验证Paddle是否安装成功

```
# 在您的Python解释器中确认PaddlePaddle安装成功
>>> import paddle
>>> paddle.utils.run_check()

# 确认PaddlePaddle版本
python -c "import paddle; print(paddle.__version__)"
```

## 安装PaddleDetection

```
# 克隆PaddleDetection仓库
cd <path/to/clone/PaddleDetection>
git clone https://github.com/PaddlePaddle/PaddleDetection.git

# 编译安装paddledet
cd PaddleDetection
python setup.py install

# 安装其他依赖
pip install -r requirements.txt
```

`pycocotools`依赖可能安装失败，可采用第三方实现版本，自己编译:

```
pip install git+https://github.com/philferriere/cocoapi.git#subdirectory=PythonAPI
```

此过程可能报错提示`error: Microsoft Visual C++ 14.0 or greater is required. Get it with .......`，此时需要安装`Microsoft Visual C++ build tool`，推荐使用离线版安装，否则会出现下载安装包速度缓慢、安装包损坏等问题。

解决所用问题后安装完所有依赖，进行测试:

```
python ppdet/modeling/tests/test_architectures.py
```

出现如下的类似信息则说明安装完成:

```
W0826 18:48:00.794705  6988 device_context.cc:404] Please NOTE: device: 0, GPU Compute Capability: 7.5, Driver API Version: 11.4, Runtime API Version: 11.2
W0826 18:48:00.816314  6988 device_context.cc:422] device: 0, cuDNN Version: 8.2.
.....
----------------------------------------------------------------------
Ran 5 tests in 2.461s

OK
```



***



# 数据集制作

目录结构：

- dataset/
  - mydataset/
    - annotation/ 存放xml格式的标注文件
    - images/ 存放原始图片文件
    - ImageSets/ 数据集索引
      - label_list.txt 存放类别标签
      - train.txt 训练集数据名称
      - val.txt 验证集数据名称
    - train.txt 最终的数据集索引格式(create_list.py制作)
    - val.txt 最终的数据集索引格式(create_list.py制作)
    - create_list.py 从ImageSets/Main/下的训练验证集制作数据索引
  - get_list.py 按比例划分训练集验证集等

**get_list.py示例：**

```python
import os
import random

train_precent = 0.9
path = './fruits'  # root path
xml = path + "/Annotations"
save = path + "/Main"
total_xml = os.listdir(xml)

num = len(total_xml)
tr = int(num * train_precent)
index = list(range(num))
random.shuffle(index)

ftrain = open(path + "/ImageSets/train.txt", "w")
fval = open(path + "/ImageSets/val.txt", "w")

for i in range(num):
    name = total_xml[index[i]][:-4] + "\n"
    if i < tr:
        ftrain.write(name)
    else:
        fval.write(name)

ftrain.close()
fval.close()
```

**create_list.py示例：**

```python
import os
import os.path as osp
import re
import random


def get_dir(devkit_dir, type):
    return osp.join(devkit_dir, type)


def walk_dir(devkit_dir):
    filelist_dir = get_dir(devkit_dir, 'ImageSets/')
    annotation_dir = get_dir(devkit_dir, 'Annotations')
    img_dir = get_dir(devkit_dir, 'JPEGImages')
    trainval_list = []
    test_list = []
    added = set()

    for _, _, files in os.walk(filelist_dir):
        for fname in files:
            img_ann_list = []
            if re.match('train\.txt', fname):
                img_ann_list = trainval_list
            elif re.match('val\.txt', fname):
                img_ann_list = test_list
            else:
                continue
            fpath = osp.join(filelist_dir, fname)
            for line in open(fpath):
                name_prefix = line.strip().split()[0]
                if name_prefix in added:
                    continue
                added.add(name_prefix)
                ann_path = osp.join(annotation_dir, name_prefix + '.xml')
                img_path = osp.join(img_dir, name_prefix + '.jpg')
                # ann_path = osp.join(name_prefix + '.xml')
                # img_path = osp.join(name_prefix + '.jpg')
                assert os.path.isfile(ann_path), 'file %s not found.' % ann_path
                assert os.path.isfile(img_path), 'file %s not found.' % img_path
                img_ann_list.append((img_path, ann_path))

    return trainval_list, test_list


def prepare_filelist(devkit_dir, output_dir):
    trainval_list = []
    test_list = []
    trainval, test = walk_dir(devkit_dir)
    trainval_list.extend(trainval)
    test_list.extend(test)
    random.shuffle(trainval_list)

    with open(osp.join(output_dir, 'train.txt'), 'w') as ftrainval:
        for item in trainval_list:
            ftrainval.write(item[0] + ' ' + item[1] + '\n')

    with open(osp.join(output_dir, 'val.txt'), 'w') as ftest:
        for item in test_list:
            ftest.write(item[0] + ' ' + item[1] + '\n')


if __name__ == '__main__':
    prepare_filelist(devkit_dir='./', output_dir='./')
```



***



# 配置文件：

在configs文件夹中选择模型，根据模型中的配置文件做相应更改：

```yaml
# ppyolo_mbv3_small_coco.yaml
_BASE_: [
  '../datasets/voc.yml',
  '../runtime.yml',
  './_base_/ppyolo_mbv3_small.yml',
  './_base_/optimizer_1x.yml',
  './_base_/ppyolo_reader.yml',
]
```

如上所示，要调用此模型，需要对其中的五个配置文件进行配置。

**数据集配置:**

```yaml
# voc.yaml
metric: VOC
map_type: 11point
num_classes: 2

TrainDataset:
  !VOCDataSet
    dataset_dir: dataset/pipes # dataset/voc
    anno_path: train.txt
    label_list: ImageSets/Main/label_list.txt
    data_fields: ['image', 'gt_bbox', 'gt_class', 'difficult']

EvalDataset:
  !VOCDataSet
    dataset_dir: dataset/pipes
    anno_path: val.txt
    label_list: ImageSets/Main/label_list.txt
    data_fields: ['image', 'gt_bbox', 'gt_class', 'difficult']

TestDataset:
  !ImageFolder
    anno_path: dataset/voc/label_list.txt
```

其他配置文件自定义修改.......



***



# 训练模型

开始训练：

```
python tools/train.py -c configs/ppyolo/ppyolo_mbv3_small_coco.yml # 单卡
python -m paddle.distributed.launch --gpus 0,1,2,3,4,5,6,7 tools/train.py -c configs/yolov3/yolov3_mobilenet_v1_roadsign.yml # 多卡
```



***



# 预测

推断图片：

```
python tools/infer.py -c configs/yolov3/yolov3_mobilenet_v1_roadsign.yml --infer_img=demo/000000570688.jpg -o weights=https://paddledet.bj.bcebos.com/models/yolov3_mobilenet_v1_roadsign.pdparams
```

设置预测参数：

```
export CUDA_VISIBLE_DEVICES=0 #windows和Mac下不需要执行该命令
python tools/infer.py -c configs/yolov3/yolov3_mobilenet_v1_roadsign.yml \
                    --infer_img=demo/road554.png \
                    --output_dir=infer_output/ \
                    --draw_threshold=0.5 \
                    -o weights=output/yolov3_mobilenet_v1_roadsign/model_final \
                    --use_vdl=Ture
```



***



# 参数解释

| FLAG                   | 支持脚本           | 用途                                                         | 默认值                                                | 备注                                                         |
| ---------------------- | ------------------ | ------------------------------------------------------------ | ----------------------------------------------------- | ------------------------------------------------------------ |
| -c                     | ALL                | 指定配置文件                                                 | None                                                  | **必选**，例如-c configs/faster_rcnn/faster_rcnn_r50_fpn_1x_coco.yml |
| -o                     | ALL                | 设置或更改配置文件里的参数内容                               | None                                                  | 相较于`-c`设置的配置文件有更高优先级，例如：`-o use_gpu=False` |
| --eval                 | train              | 是否边训练边测试                                             | False                                                 | 如需指定，直接`--eval`即可                                   |
| -r/--resume_checkpoint | train              | 恢复训练加载的权重路径                                       | None                                                  | 例如：`-r output/faster_rcnn_r50_1x_coco/10000`              |
| --slim_config          | ALL                | 模型压缩策略配置文件                                         | None                                                  | 例如`--slim_config configs/slim/prune/yolov3_prune_l1_norm.yml` |
| --use_vdl              | train/infer        | 是否使用[VisualDL](https://github.com/paddlepaddle/visualdl)记录数据，进而在VisualDL面板中显示 | False                                                 | VisualDL需Python>=3.5                                        |
| --vdl_log_dir          | train/infer        | 指定 VisualDL 记录数据的存储路径                             | train:`vdl_log_dir/scalar` infer: `vdl_log_dir/image` | VisualDL需Python>=3.5                                        |
| --output_eval          | eval               | 评估阶段保存json路径                                         | None                                                  | 例如 `--output_eval=eval_output`, 默认为当前路径             |
| --json_eval            | eval               | 是否通过已存在的bbox.json或者mask.json进行评估               | False                                                 | 如需指定，直接`--json_eval`即可， json文件路径在`--output_eval`中设置 |
| --classwise            | eval               | 是否评估单类AP和绘制单类PR曲线                               | False                                                 | 如需指定，直接`--classwise`即可                              |
| --output_dir           | infer/export_model | 预测后结果或导出模型保存路径                                 | `./output`                                            | 例如`--output_dir=output`                                    |
| --draw_threshold       | infer              | 可视化时分数阈值                                             | 0.5                                                   | 例如`--draw_threshold=0.7`                                   |
| --infer_dir            | infer              | 用于预测的图片文件夹路径                                     | None                                                  | `--infer_img`和`--infer_dir`必须至少设置一个                 |
| --infer_img            | infer              | 用于预测的图片路径                                           | None                                                  | `--infer_img`和`--infer_dir`必须至少设置一个，`infer_img`具有更高优先级 |
| --save_txt             | infer              | 是否在文件夹下将图片的预测结果保存到文本文件中               | False                                                 | 可选                                                         |

