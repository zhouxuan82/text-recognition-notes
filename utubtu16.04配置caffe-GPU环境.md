---
typora-copy-images-to: image
---

# Ubuntu16.04配置caffe-GPU环境



[TOC]



## 1 安装NVIDIA driver、CUDA Toolkit和cuDNN

[caffe官网](http://caffe.berkeleyvision.org/install_apt.html)建议显卡驱动和CUDA工具包分别单独安装，理由是英伟达官网下载的[CUDA安装包](https://developer.nvidia.com/cuda-80-ga2-download-archive)(.deb包或者,run包)中自带的显卡驱动往往都已经过时了，最好单独安装最新的显卡驱动。

**安装显卡驱动**有一个最简单的方法：*系统设置 -->软件与更新 -->附加驱动*界面，可以使用比较新的专有的英伟达显卡驱动：

![Screenshot from 2018-01-20 13-45-30](image/Screenshot from 2018-01-20 13-45-30.png)

注意第二个未知驱动不要使用它，它貌似是一个开源的英特尔集成显卡驱动。按图中方式选择好后点击*应用更改*，系统就会自动下载英伟达显卡驱动进行安装，安装好之后重启系统。

**安装CUDA Toolkit**，网上的教程大都使用.run文件的安装方式，但其实使用本地.deb文件进行安装更简单：

首先从[官网](https://developer.nvidia.com/cuda-80-ga2-download-archive)下载CUDA本地.deb安装包：

![Screenshot from 2018-01-20 13-53-29](image/Screenshot from 2018-01-20 13-53-29-6427651060.png)

这里下载的是CUDA8.0.61，如上图所示，除了一个1.9G大小的主安装包，下面还有一个121.4MB的补充安装包，都下载下来，得到如下两个文件：

```
cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64.deb
cuda-repo-ubuntu1604-8-0-local-cublas-performance-update_8.0.61-1_amd64.deb
```

安装过程也十分简单，就按照上图下载界面中的*Installation Instruction*，先依次执行以下命令安装主安装包：

```
sudo dpkg -i cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64.deb
sudo apt-get update
sudo apt-get install cuda
```

前两步实际上添加了一个本地源仓库，所以直接使用apt命令安装。

再**安装cuDNN**，caffe官网说，目前从Github上clone的caffe版本需要V6版本的cuDNN，所以不能安装网上那些教程那样使用V5.1的版本了。

在[这里](https://developer.nvidia.com/rdp/cudnn-download)下载cuDNN v6.0安装包，下载下图中两个.deb包：

![Screenshot from 2018-01-20 14-07-37](image/Screenshot from 2018-01-20 14-07-37.png)

下载得到的文件名为：

```
libcudnn6_6.0.21-1+cuda8.0_amd64.deb
libcudnn6-dev_6.0.21-1+cuda8.0_amd64.deb
```

直接使用dpkg命令安装这两个包：

```
sudo dpkg -i libcudnn6_6.0.21-1+cuda8.0_amd64.deb
sudo dpkg -i libcudnn6-dev_6.0.21-1+cuda8.0_amd64.deb
```

安装完重启电脑。

NVIDIA driver、CUDA Toolkit和cuDNN安装完毕。



## 2 从源代码编译Opencv3.3

这部分内容参考自：[OpenCV 3.3 Installation Guide on Ubuntu 16.04](https://github.com/BVLC/caffe/wiki/OpenCV-3.3-Installation-Guide-on-Ubuntu-16.04)。

另外可以参考opencv官方的安装文档：[Install OpenCV-Python in Ubuntu](https://docs.opencv.org/master/d2/de6/tutorial_py_setup_in_ubuntu.html)。

还有一篇也是官方的参考文档：[Installation in Linux](https://docs.opencv.org/master/d7/d9f/tutorial_linux_install.html)。

先依次使用以下命令安装必要的依赖项(其中某些应该是不需要的，但都先装上吧)：

```
sudo apt-get install -y build-essential cmake git pkg-config
sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler libatlas-base-dev libboost-all-dev libgflags-dev libgoogle-glog-dev liblmdb-dev

sudo apt-get install -y python-pip
sudo apt-get install -y libopencv-dev

sudo apt-get install --assume-yes pkg-config unzip ffmpeg qtbase5-dev python-dev python3-dev python-numpy python3-numpy

sudo apt-get install --assume-yes libopencv-dev libgtk-3-dev libdc1394-22 libdc1394-22-dev libjpeg-dev libpng12-dev libtiff5-dev libjasper-dev

sudo apt-get install --assume-yes libavcodec-dev libavformat-dev libswscale-dev libxine2-dev libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev

sudo apt-get install --assume-yes libv4l-dev libtbb-dev libfaac-dev libmp3lame-dev libopencore-amrnb-dev libopencore-amrwb-dev libtheora-dev

sudo apt-get install --assume-yes libvorbis-dev libxvidcore-dev v4l-utils vtk6
sudo apt-get install --assume-yes liblapacke-dev libopenblas-dev libgdal-dev checkinstall
```

如果安装过程中某些安装包因为HASH值不匹配无法获取，就加上`--fix-missing`命令安装。

这里使用的是系统自带的python2.7，不另外安装anconda2了。

从[这里](https://github.com/opencv/opencv/archive/3.3.0.zip)下载python-3.3,0.zip源代码文件。

下载下来后，使用：

```
unzip opencv-3.3.0.zip
```

将其解压，为了简单，将得到的opencv-3.3.0文件夹重命名为opencv文件夹。

然后从命令行进入opencv文件，执行以下命令：

```
mkdir build
cd build/ 

cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D FORCE_VTK=ON -D WITH_TBB=ON -D WITH_V4L=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D WITH_CUBLAS=ON -D CUDA_NVCC_FLAGS="-D_FORCE_INLINES" -D WITH_GDAL=ON -D WITH_XINE=ON -D BUILD_EXAMPLES=ON ..

make -j8
```

这是一个漫长的编译过程，大概需要约半小时，如果一切顺利，最后会输出下图的结果：

![Screenshot from 2018-01-19 16-10-55](image/Screenshot from 2018-01-19 16-10-55.png)

继续执行以下命令：

```
sudo make install
sudo /bin/bash -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf'
sudo ldconfig
sudo apt-get update
```

sudo make install完成时，如果没出错应该如下图的输出：

![Screenshot from 2018-01-19 16-16-34](image/Screenshot from 2018-01-19 16-16-34.png)

执行完上面步骤之后，重启电脑。

opencv3.3编译完成。



## 3 安装必要的依赖项

官网的caffe安装教程中，编译caffe前需要安装以下依赖项：

```
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
sudo apt-get install libatlas-base-dev
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
```

但其实上一步编译opencv时这些依赖项都已经安装过了，所以这一步可以直接跳过。



## 4 编译caffe

首先从Github上面下载caffe源代码：

```
git clone https://github.com/BVLC/caffe.git
```

参考这篇教程：[Ubuntu 16.04 Installation Guide](https://github.com/BVLC/caffe/wiki/Ubuntu-16.04-Installation-Guide#the-gpu-support-prerequisites)，文中说道：

```
Go to the https://github.com/BVLC/caffe and download the zip archive. Unpack it to ~/bin/ or any other location. Enter the caffe-master directory in the terminal window. Note that only the version 1.0RC5 compiles well at this moment, so download the 1.0RC5 version from https://github.com/BVLC/caffe/archive/rc5.zip. If you download the 1.0 version from https://github.com/BVLC/caffe/archive/1.0.zip, you need to edit the file /src/caffe/util/blocking_queue.cpp. After the line 89, and the new line that contains the following:
template class BlockingQueue<Datum*>;
```

以防万一，就按照它说的，打开`caffe/src/caffe/util/blocking_queue.cpp`文件，在89行后面加上：

```
template class BlockingQueue<Datum*>;
```

首先**修改`Makefile.config`文件**，在命令行进入caffe主目录，执行

```
cp Makefile.config.example Makefile.config
```

使用atom打开`Makefile.config`配置文件：

```
sudo atom Makefile.config
```

修改过后的`Makefile.config`文件如下：

```
## Refer to http://caffe.berkeleyvision.org/installation.html
# Contributions simplifying and improving our build system are welcome!

# cuDNN acceleration switch (uncomment to build with cuDNN).
USE_CUDNN := 1

# CPU-only switch (uncomment to build without GPU support).
# CPU_ONLY := 1

# uncomment to disable IO dependencies and corresponding data layers
# USE_OPENCV := 0
# USE_LEVELDB := 0
# USE_LMDB := 0

# uncomment to allow MDB_NOLOCK when reading LMDB files (only if necessary)
#	You should not set this flag if you will be reading LMDBs with any
#	possibility of simultaneous read and write
# ALLOW_LMDB_NOLOCK := 1

# Uncomment if you're using OpenCV 3
OPENCV_VERSION := 3

# To customize your choice of compiler, uncomment and set the following.
# N.B. the default for Linux is g++ and the default for OSX is clang++
# CUSTOM_CXX := g++

# CUDA directory contains bin/ and lib/ directories that we need.
CUDA_DIR := /usr/local/cuda
# On Ubuntu 14.04, if cuda tools are installed via
# "sudo apt-get install nvidia-cuda-toolkit" then use this instead:
# CUDA_DIR := /usr

# CUDA architecture setting: going with all of them.
# For CUDA < 6.0, comment the *_50 through *_61 lines for compatibility.
# For CUDA < 8.0, comment the *_60 and *_61 lines for compatibility.
# For CUDA >= 9.0, comment the *_20 and *_21 lines for compatibility.
CUDA_ARCH :=
#		-gencode arch=compute_20,code=sm_20 \
#		-gencode arch=compute_20,code=sm_21 \
#		-gencode arch=compute_30,code=sm_30 \
		-gencode arch=compute_35,code=sm_35 \
		-gencode arch=compute_50,code=sm_50 \
		-gencode arch=compute_52,code=sm_52 \
		-gencode arch=compute_60,code=sm_60 \
		-gencode arch=compute_61,code=sm_61 \
		-gencode arch=compute_61,code=compute_61

# BLAS choice:
# atlas for ATLAS (default)
# mkl for MKL
# open for OpenBlas
BLAS := atlas
# Custom (MKL/ATLAS/OpenBLAS) include and lib directories.
# Leave commented to accept the defaults for your choice of BLAS
# (which should work)!
# BLAS_INCLUDE := /path/to/your/blas
# BLAS_LIB := /path/to/your/blas

# Homebrew puts openblas in a directory that is not on the standard search path
# BLAS_INCLUDE := $(shell brew --prefix openblas)/include
# BLAS_LIB := $(shell brew --prefix openblas)/lib

# This is required only if you will compile the matlab interface.
# MATLAB directory should contain the mex binary in /bin.
# MATLAB_DIR := /usr/local
# MATLAB_DIR := /Applications/MATLAB_R2012b.app

# NOTE: this is required only if you will compile the python interface.
# We need to be able to find Python.h and numpy/arrayobject.h.
PYTHON_INCLUDE := /usr/include/python2.7 \
		/usr/lib/python2.7/dist-packages/numpy/core/include
# Anaconda Python distribution is quite popular. Include path:
# Verify anaconda location, sometimes it's in root.
# ANACONDA_HOME := $(HOME)/anaconda
# PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
		# $(ANACONDA_HOME)/include/python2.7 \
		# $(ANACONDA_HOME)/lib/python2.7/site-packages/numpy/core/include

# Uncomment to use Python 3 (default is Python 2)
# PYTHON_LIBRARIES := boost_python3 python3.5m
# PYTHON_INCLUDE := /usr/include/python3.5m \
#                 /usr/lib/python3.5/dist-packages/numpy/core/include

# We need to be able to find libpythonX.X.so or .dylib.
PYTHON_LIB := /usr/lib
# PYTHON_LIB := $(ANACONDA_HOME)/lib

# Homebrew installs numpy in a non standard path (keg only)
# PYTHON_INCLUDE += $(dir $(shell python -c 'import numpy.core; print(numpy.core.__file__)'))/include
# PYTHON_LIB += $(shell brew --prefix numpy)/lib

# Uncomment to support layers written in Python (will link against Python libs)
WITH_PYTHON_LAYER := 1

# Whatever else you find you need goes here.
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial

# If Homebrew is installed at a non standard location (for example your home directory) and you use it for general dependencies
# INCLUDE_DIRS += $(shell brew --prefix)/include
# LIBRARY_DIRS += $(shell brew --prefix)/lib

# NCCL acceleration switch (uncomment to build with NCCL)
# https://github.com/NVIDIA/nccl (last tested version: v1.2.3-1+cuda8.0)
# USE_NCCL := 1

# Uncomment to use `pkg-config` to specify OpenCV library paths.
# (Usually not necessary -- OpenCV libraries are normally installed in one of the above $LIBRARY_DIRS.)
# USE_PKG_CONFIG := 1

# N.B. both build and distribute dirs are cleared on `make clean`
BUILD_DIR := build
DISTRIBUTE_DIR := distribute

# Uncomment for debugging. Does not work on OSX due to https://github.com/BVLC/caffe/issues/171
# DEBUG := 1

# The ID of the GPU that 'make runtest' will use to run unit tests.
TEST_GPUID := 0

# enable pretty build (comment to see full commands)
Q ?= @
```

接着**修改Makefile文件**，将大约415行处的：

```
NVCCFLAGS += -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS)
```

替换为：

```
NVCCFLAGS += -D_FORCE_INLINES -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS)
```

继续**修改`CMakeLists.txt` 文件**，在最后面添加上一下语句：

```
# ---[ Includes
set(${CMAKE_CXX_FLAGS} "-D_FORCE_INLINES ${CMAKE_CXX_FLAGS}")
```

最后**修改`/usr/local/cuda/include/host_config.h` 文件**，使用atom打开：

```
sudo atom /usr/local/cuda/include/host_config.h 
```

将以下这句：

```
#error-- unsupported GNU version! gcc versions later than 5 are not supported!
```

改为：

```
//#error-- unsupported GNU version! gcc versions later than 5 are not supported!
```

也就是把它注释掉。这一步如果不注释掉的话，就要像很多网上教程那样，手动将gcc版本降级。

准备工作完毕，依次执行以下命令编译caffe：

```
make all -j8
make test -j8
make runtest -j8
make pycaffe -j8 #编译pycaffe接口
make distribute -j8
```

加上`-j8`能大大提高编译过程，这是利用了多核处理器的并行同步执行的优点。

最后为了能在python中import caffe，需要在~/.bashrc中添加以下路径：

```
export PYTHONPATH=/home/yan/caffe/python:$PYTHONPATH
```

这样就能顺利import caffe了：

```
yan@yanubuntu:~$ python
Python 2.7.12 (default, Dec  4 2017, 14:50:18) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import caffe
>>> 
```



## 5 MNIST数据集测试

在caffe主目录下打开终端，执行脚本：

```
bash data/mnist/get_mnist.sh
```

这个脚本的功能是从网上下载MNIST数据集二进制文件，执行完后，在`data/mnist/`下面得到如下4个文件：

![Screenshot from 2018-01-20 15-15-30](image/Screenshot from 2018-01-20 15-15-30.png)

得到数据集之后，接着需要将二进制的数据集格式转换成caffe需要的LMDB格式。

执行以下命令：

```
bash example/mnist/create_mnist.sh
```

在`data/mnist/`文件夹下得到两个LMDB格式的数据集文件：

![Screenshot from 2018-01-20 15-22-14](image/Screenshot from 2018-01-20 15-22-14-6433007234.png)

接着执行：

```
bash example/mnist/train_lenet.sh
```

如果一切顺利，系统就开始训练模型了，最终会得到大约0.99的准确率。

MNIST数据集测试完成。







