---
title: python函数调用关系分析
math: true
excerpt: -----
photos:
  -	https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20211213pyviz0.png
date: 2021-12-13 15:45:07
tags:
  -	Tools
  -	Python
  -	随笔
categories:
  -	[Tools]
  -	[随笔]
---





在Pycharm的专业版中，提供了一个分析项目性能的工具`run->Profile`，可以将工程运行时各函数的调用次数和所用时间记录下来，提供静态数据和关系图表两种表达方式。

![](https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20211213pyviz1.png)

只需轻轻一点，统计数据和图表关系一览无遗，确实方便。但是此方法局限性很大，一是必须使用pycharm，这样使用其他ide如VS Code的用户就没法使用了；二是它不仅要使用pychram，还得是专业版，专业版是要收费的(破解和学生版除外)，这又是一道坎挡在了我们的路上。那么有其他的方法来进行类似的分析吗？当然是有的。



---



# profile

profile可以分析函数运行时的性能消耗情况，调用方法如下：

```python
import profile
profile.run("main()")
```

main()即为需要分析的入口函数，以字符串形式传入。运行之后可以得到如下的输出：

```
19 function calls in 0.000 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.000    0.000    0.000    0.000 :0(exec)
        4    0.000    0.000    0.000    0.000 :0(len)
        4    0.000    0.000    0.000    0.000 :0(print)
        1    0.000    0.000    0.000    0.000 :0(setprofile)
        2    0.000    0.000    0.000    0.000 :0(sleep)
        1    0.000    0.000    0.000    0.000 <string>:1(<module>)
        1    0.000    0.000    0.000    0.000 profile:0(main())
        0    0.000             0.000          profile:0(profiler)
        1    0.000    0.000    0.000    0.000 test.py:1(next_cal)
        1    0.000    0.000    0.000    0.000 test.py:16(__init__)
        1    0.000    0.000    0.000    0.000 test.py:19(search_string)
        1    0.000    0.000    0.000    0.000 test.py:36(__next_arr)
        1    0.000    0.000    0.000    0.000 test.py:51(main)
```

虽然函数调用情况可以得到的，但是分析起来比较麻烦，函数之间的调用关系也没有给出。此方式虽然简单但是不够直观。



---



# pycallgraph

在pycallgraph之前不得不先提及一下**[Graphviz](http://www.graphviz.org/)**，毕竟pycallgraph是基于Graphviz的。Graphviz是开源的图形可视化软件，能够将结构信息表示为抽象图和网络图的方法。至官网下载并安装Graphviz：[http://www.graphviz.org/download/](http://www.graphviz.org/download/)，同时在将`graphviz\bin`添加到环境变量中。graphviz中有多种布局器，pycallgraph所使用的便是其中的dot.

```
dot 默认布局方式，主要用于有向图
neato 基于spring-model(又称force-based)算法
twopi 径向布局
circo 圆环布局
fdp 用于无向图
```

看看graphviz都能画些什么样的图：

![](https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20211213pyviz2.png)

![](https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20211213pyviz3.png)

安装并配置好graphviz后，需要安装pycallgraph，直接使用pip安装即可:

```
pip install pycallgraph
```

之后可以通过命令行或代码来生成函数调用关系图。

命令行(windows中不识别pycallgraph，应该还要需要配置其环境变量):

```
pycallgraph graphviz -- ./main.py
```

code:

```python
from pycallgraph import PyCallGraph
from pycallgraph.output import GraphvizOutput

with PyCallGraph(output=GraphvizOutput(output_file="test.png")):
    main()
```

当然可以配置显示或不显示哪些函数：

```python
from pycallgraph import Config
from pycallgraph import PyCallGraph
from pycallgraph.output import GraphvizOutput
config = Config()
# 关系图中包括(include)哪些函数名。
# 如果是某一类的函数，例如类gobang，则可以直接写'gobang.*'，表示以gobang.开头的所有函数。（利用正则表达式）。
# config.trace_filter = GlobbingFilter(include=[
#     'draw_chessboard',
#     'draw_chessman',
#     'draw_chessboard_with_chessman',
#     'choose_save',
#     'choose_turn',
#     'choose_mode',
#     'choose_button',
#     'save_chess',
#     'load_chess',
#     'play_chess',
#     'pop_window',
#     'tip',
#     'get_score',
#     'max_score',
#     'win',
#     'key_control'
# ])
# 该段作用是关系图中不包括(exclude)哪些函数。(正则表达式规则)
# config.trace_filter = GlobbingFilter(exclude=[
#     'pycallgraph.*',
#     '*.secret_function',
#     'FileFinder.*',
#     'ModuleLockManager.*',
#     'SourceFilLoader.*'
# ])
graphviz = GraphvizOutput()
graphviz.output_file = 'test.png'
with PyCallGraph(output=graphviz, config=config):
	main()
```

运行后再当前目录下生成test.png文件绘制了函数调用关系(默认文件名是pycallgraph.png)：

```python
class GraphvizOutput(Output):

    def __init__(self, **kwargs):
        self.tool = 'dot'
        self.output_file = 'pycallgraph.png'
        self.output_type = 'png'
        self.font_name = 'Verdana'
        self.font_size = 7
        self.group_font_size = 10
        self.group_border_color = Color(0, 0, 0, 0.8)

        Output.__init__(self, **kwargs)

        self.prepare_graph_attributes()
```

![](https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20211213pyviz4.png)

可见此方式对函数的调用关系展示得较为清晰明了，其缺点是制作的图分辨率不高，略显模糊。下为yolov5的调用关系图。

![](https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20211213pyviz5.png)

# cProfile+snakeviz

使用**cProfie**可以将函数运行分析结果保存为.prof文件，然后使用其他工具进行可视化，此处以**snakeviz**为例。

## cProfile

使用cProfile分析工程，可以使用命令行和代码两种方式。

- cmd

```
python -m cProfile -o result.prof test.py
```

- code

```python
import cProfile
cProfile.run("main()", filename='result.prof')
```

不推荐使用命令行形式，针对，整个文件，会打包过多的无用函数，影响观感。使用code方式针对特定函数分析即可。

# snakeviz

安装snakeviz:

```
pip install snakeviz
```

直接调用cProfile生成的文件，在浏览器中可以打开交互式网页，查看分析结果。缺点：虽然能够从图中获知函数调用顺序，但是函数调用关系依旧不够直观。



![](https://gitlab.com/XiubenWu/xiubenwu-images/-/raw/master/img/20211213pyviz6.png)

