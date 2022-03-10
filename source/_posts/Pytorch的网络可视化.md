---
title: Pytorch的网络可视化
excerpt: -----
photos:
  -	https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20220310pytorchnet0.jpg
date: 2022-03-10 18:08:46
tags:
  - 随笔
categories:
  -	随笔
---

# 概述
有时候想要绘制一个神经网络的网络结构，可以通过代码中层的定义去用第三方软件依次绘制，但是这样费时费力。因此需要一种快速绘制的办法。



---



# Netron

netron可通过所保存的模型将其用网络的方式可视化出来，但是对于pytorch来说，其支持程度还不够，无法绘制各参数间的关系，可以将pytorch模型导出为onnx格式再使用netron来可视化。

```python
# 自建一个CNN模型并导出为onnx来可视化
import torch
import torch.nn as nn

class CNNModel(nn.Module):
    def __init__(self, out_channels=10):
        super(CNNModel, self).__init__()
        self.conv = nn.Sequential(
            nn.Conv2d(3, 16, kernel_size=(5, 5), stride=(3, 3), padding=0),
            nn.LeakyReLU(0.2, inplace=True),
            nn.BatchNorm2d(16),
            nn.MaxPool2d(2),

            nn.Conv2d(16, 32, kernel_size=(5, 5), stride=(3, 3), padding=0),
            nn.LeakyReLU(0.2, inplace=True),
            nn.BatchNorm2d(32),
            nn.MaxPool2d(2),

            nn.Conv2d(32, 1, kernel_size=(3, 3), stride=(2, 2), padding=0)
        )
        self.ful_layer = nn.Sequential(
            nn.Linear(36, 16),
            nn.LeakyReLU(0.2, inplace=True),
            nn.BatchNorm1d(16),

            nn.Linear(16, out_channels),
            nn.Softmax(dim=1)
        )

    def forward(self, x):
        x = self.conv(x)
        x = x.view(x.size(0), -1)
        x = self.ful_layer(x)
        return x


model = CNNModel()
x = torch.rand(16, 3, 512, 512)
torch.onnx.export(model, x, "CNN.onnx")
```

- 用netron打开后的模型结果：

![](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20220310pytorchnet1.png)



---



# torchviz

使用netron可以直观的显示出网络的结构，但是需要对模型进行转换，在转换的过程中也许会遇到模块的不兼容等问题，修改麻烦，使用torchviz也可以快速的生成模型结构图。要使用torchviz需要先安装[Graphviz](https://www.graphviz.org/)，这个工具在之前的[文章](https://www.xiubenwu.top/2021/12/13/python函数调用关系分析/)中也有提及。安装后的使用方式也非常简单，依旧以简单CNN网络为例：

```python
import torch
import torch.nn as nn
import torchviz


class CNNModel(nn.Module):
    def __init__(self, out_channels=10):
        super(CNNModel, self).__init__()
        self.conv = nn.Sequential(
            nn.Conv2d(3, 16, kernel_size=(5, 5), stride=(3, 3), padding=0),
            nn.LeakyReLU(0.2, inplace=True),
            nn.BatchNorm2d(16),
            nn.MaxPool2d(2),

            nn.Conv2d(16, 32, kernel_size=(5, 5), stride=(3, 3), padding=0),
            nn.LeakyReLU(0.2, inplace=True),
            nn.BatchNorm2d(32),
            nn.MaxPool2d(2),

            nn.Conv2d(32, 1, kernel_size=(3, 3), stride=(2, 2), padding=0)
        )
        self.ful_layer = nn.Sequential(
            nn.Linear(36, 16),
            nn.LeakyReLU(0.2, inplace=True),
            nn.BatchNorm1d(16),

            nn.Linear(16, out_channels),
            nn.Softmax(dim=1)
        )

    def forward(self, x):
        x = self.conv(x)
        x = x.view(x.size(0), -1)
        x = self.ful_layer(x)
        return x


model = CNNModel()
x = torch.rand(16, 3, 512, 512)
out = model(x)
g = torchviz.make_dot(out)
g.render("test")

```

- 最终可以生成PDF文件；

![](https://gitee.com/xiubenwu/xiubenwu-images/raw/master/img/20220310pytorchnet2.png)

模型结构相较于netron来说更为冗杂。



