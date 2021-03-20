---
title: Pycharm之库的导入顺序？
math: true
date: 2021-03-20 14:30:46
excerpt: 戳阅读全文&dArr;
tags:
    - [Python]
categories:
    - [爬坑]
---

使用python时遇到了一个问题:`Cannot mix incompatible Qt library (version 0x50907) with this library (version 0x50a01)`，通过字面的意思来看这是因为qt版本冲突，网上找了很多解决方法都是删库，这个处理方式着实不方便，后来在一位博主出找到了另一种[解决方式](https://blog.csdn.net/dream_allday/article/details/95967312)。   
以下是冲突代码：

```python
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
import torch
import matplotlib.ticker as ticker
```

更改后的无报错代码:
```python
import torch
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd
import matplotlib.ticker as ticker
```

对比两版代码的区别可以发现把torch这个库的导入次序排到前面就可以避免报错了， ***Amazing！*** 。原来，python的库导入竟然是有讲究的，安排不恰当就有可能报错。   
其实遇到这种情况的几率较小，但遇到时何妨不尝试换换导入顺序 **(特别是将torch库提前)** ，总比删库的解决方式更为简单实用些。