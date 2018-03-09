---
typora-copy-images-to: image
---

# 使用draw_net.py绘制caffe模型图

成功编译完pycaffe后，在`caffe/python/`文件夹下面有一个**draw_net.py**脚本，可以使用它来绘制caffe的网络模型图。如何操作？

在caffe/python/目录下打开bash，输入以下命令：

```
python draw_net.py ../examples/mnist/lenet.prototxt lenet.png
```

也就是运行draw_net.py文件，后面的两个参数分别是要绘制的caffe模型的路径，以及输出的模型图的文件名。

但是运行后可能会出现以下错误：

```shell
ys@ysubuntu:~/caffe/python$ python draw_net.py ../examples/mnist/lenet.prototxt lenet.png
Traceback (most recent call last):
  File "draw_net.py", line 9, in <module>
    import caffe.draw
  File "/home/ys/caffe/python/caffe/draw.py", line 22, in <module>
    import pydot
ImportError: No module named pydot
```

原来需要的一个python模块pydot没安装，于是使用以下命令安装pydot：

```
sudo -H pip install pydot    #这里使用pip安装文件必须要取得管理员权限，所以加上sudo -H
```

安装好pydot之后，再次运行：

```
python draw_net.py ../examples/mnist/lenet.prototxt lenet.png
```

可能又会出现这一个错误：

```shell
ys@ysubuntu:~/caffe/python$ python draw_net.py ../examples/mnist/lenet.prototxt lenet.png
Drawing net to lenet.png
Traceback (most recent call last):
  File "draw_net.py", line 62, in <module>
    main()
  File "draw_net.py", line 58, in main
    phase, args.display_lrm)
  File "/home/ys/caffe/python/caffe/draw.py", line 314, in draw_net_to_file
    fid.write(draw_net(caffe_net, rankdir, ext, phase, display_lrm))
  File "/home/ys/caffe/python/caffe/draw.py", line 290, in draw_net
    display_lrm=display_lrm).create(format=ext)
  File "/usr/local/lib/python2.7/dist-packages/pydot.py", line 1867, in create
    raise OSError(*args)
OSError: [Errno 2] "dot" not found in path.
```

在网上搜索`OSError: [Errno 2] "dot" not found in path.`的解决办法。参考[这篇文章](http://blog.csdn.net/u011339825/article/details/53425744)，说是需要使用apt安装一个名为graphviz的包，于是：

```
sudo apt install graphviz
```

安装好graphviz之后，再次运行：

```
python draw_net.py ../examples/mnist/lenet.prototxt lenet.png
```

大功告成！

```
ys@ysubuntu:~/caffe/python$ python draw_net.py ../examples/mnist/lenet.prototxt lenet.png
Drawing net to lenet.png
ys@ysubuntu:~/caffe/python$
```

看一下得到的模型图长什么样子：

![Screenshot from 2018-03-09 09-19-35](https://github.com/YanShuang17/text-recognition-notes/blob/master/image/Screenshot%20from%202018-03-09%2009-19-35.png?raw=true)
