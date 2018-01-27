# 编译caffe－Makefile.config解析



## 配置cuDNN

原始代码：

```
# cuDNN acceleration switch (uncomment to build with cuDNN).
# USE_CUDNN := 1
```

如果要使用GPU版本的caffe并且准备使用cuDNN加速库，那就将

```
# USE_CUDNN := 1
```

改为

```
USE_CUDNN := 1
```



##  CPU or GPU

原始代码：

```
# CPU-only switch (uncomment to build without GPU support).
# CPU_ONLY := 1
```

这两行代码决定是否配置CPU版本的caffe。配置文件默认是编译GPU版本的caffe，如果电脑上没有英伟达GPU或者只准备用caffe做简单练习，则可以只编译CPU版本的caffe，将

```
# CPU_ONLY := 1
```

改成

```
CPU_ONLY := 1
```

这样，文件中所有关于CUDA和cuDNN的配置都将无效。



## 配置基本I/O依赖项

原始代码：

```
# uncomment to disable IO dependencies and corresponding data layers
# USE_OPENCV := 0
# USE_LEVELDB := 0
# USE_LMDB := 0

# uncomment to allow MDB_NOLOCK when reading LMDB files (only if necessary)
#	You should not set this flag if you will be reading LMDBs with any
#	possibility of simultaneous read and write
# ALLOW_LMDB_NOLOCK := 1

# Uncomment if you're using OpenCV 3
# OPENCV_VERSION := 3
```

这几行代码是配置caffe的基本输入/输出中用到的３个模块：opencv、LEVELDB和LMDB。

opencv是世界上最流行的开源计算机视觉库，caffe使用opencv完成一些图像存取和预处理功能，《21天实战caffe》里面说到，其实caffe里面用到的opencv模块非常有限，仅限于图片读写、图片缩放等CPU上的模块，编译配置时的配置选项其实不用选太多，可以禁用很多模块节省编译时间。

LMDB（Lightning Memory-Mapped Database Manager，闪电般的内存映射型数据库管理器），在caffe中的主要作用是提供数据管理，将形形色色的原始数据(图片、二进制数据等)转换为统一的Key-Value存储，便于caffe的DataLayer获取这些数据。

LEVELDB是caffe早期版本使用的数据存储方式，目前大部分例程都已经使用LMDB代替了LEVELDB，但是为了与以前的版本兼容，默认还是将LEVELDB依赖库编译到caffe中。

默认情况下，这三个模块都开启的，不需要修改什么。

只需要注意的是，如果你使用的是opencv3.x版本，则需要将

```
# OPENCV_VERSION := 3
```

改为

```
OPENCV_VERSION := 3
```



## 配置编译器

原始代码：

```
# To customize your choice of compiler, uncomment and set the following.
# N.B. the default for Linux is g++ and the default for OSX is clang++
# CUSTOM_CXX := g++
```

这几行代码是选择使用哪种编译器，linux默认使用g++，这里一般不用修改。



## 配置CUDA

原始代码：

```
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
		-gencode arch=compute_20,code=sm_20 \
		-gencode arch=compute_20,code=sm_21 \
		-gencode arch=compute_30,code=sm_30 \
		-gencode arch=compute_35,code=sm_35 \
		-gencode arch=compute_50,code=sm_50 \
		-gencode arch=compute_52,code=sm_52 \
		-gencode arch=compute_60,code=sm_60 \
		-gencode arch=compute_61,code=sm_61 \
		-gencode arch=compute_61,code=compute_61
```





## 配置BLAS

原始代码：

```
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
```

这一段配置代码的功能是选择BLAS库。什么是BLAS库？BLAS(Basic Linear Algebra Subprograms, 基本线性代数子程序)是caffe在实现卷积神经网络中的矩阵/向量等线性运算中使用的数学库，最常用的BLAS库有三个：Intel MKL、ATLAS和OpenBLAS。caffe可以通过上面这段配置代码，可以选择其中任何一种BLAS:

配置代码默认选择的是ATLAS，所以如果不修改上面这段代码我们就默认使用ATLAS库。

如果想使用MKL库，则需要将

```
BLAS := atlas
```

修改为

```
BLAS := mkl
```

如果要使用OpenBlas库，则需将

```
BLAS := atlas
```

修改为

```
BLAS := open
```

此外，如果不适用默认的ATLAS，同时还需要添加相应模块的lib路径和include路径：

```
BLAS_INCLUDE := /path/to/your/blas
BLAS_LIB := /path/to/your/blas
```

例如，在py-faster-rcnn的python源代码中，作者提供的配置文件中就选择的是OpenBlas模块，下面是其对应配置代码：

```
# BLAS choice:
# atlas for ATLAS (default)
# mkl for MKL
# open for OpenBlas
BLAS := open
# Custom (MKL/ATLAS/OpenBLAS) include and lib directories.
# Leave commented to accept the defaults for your choice of BLAS
# (which should work)!
BLAS_INCLUDE := /opt/OpenBLAS/include
BLAS_LIB := /opt/OpenBLAS/lib
```



最下面的Homebrew是MAC苹果系统中的包管理系统，类似于apt-get。我们使用的linux系统，所以这一段不用管它。



## 配置caffe-matlab接口

原始代码：

```
# This is required only if you will compile the matlab interface.
# MATLAB directory should contain the mex binary in /bin.
# MATLAB_DIR := /usr/local
# MATLAB_DIR := /Applications/MATLAB_R2012b.app
```

默认情况下，配置文件只编译caffe的python接口，不编译matlab接口，如果你要使用matlab进行开发，需要对这一部分进行修改。



## 配置caffe-python接口

原始代码：

```
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
# WITH_PYTHON_LAYER := 1
```





## 配置其他依赖项的lib路径和include路径

原始代码：

```
# Whatever else you find you need goes here.
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib

# If Homebrew is installed at a non standard location (for example your home directory) and you use it for general dependencies
# INCLUDE_DIRS += $(shell brew --prefix)/include
# LIBRARY_DIRS += $(shell brew --prefix)/lib
```

除了上面提到的几个依赖项，caffe还有其他几个必须的依赖项，这里的include路径和lib路径就是给出其他caffe依赖项的对应位置。

一般需要将

```
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib
```

改为

```
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial
```

Homebrew下面部分属于苹果系统的配置选项，同样不用管它。

##　其他配置

原始代码:

```
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

。。。























## py-faster-rcnn作者的Makefile.config

```
## Refer to http://caffe.berkeleyvision.org/installation.html
# Contributions simplifying and improving our build system are welcome!

# cuDNN acceleration switch (uncomment to build with cuDNN).
USE_CUDNN := 1

# CPU-only switch (uncomment to build without GPU support).
# CPU_ONLY := 1

# To customize your choice of compiler, uncomment and set the following.
# N.B. the default for Linux is g++ and the default for OSX is clang++
CUSTOM_CXX := g++-4.7

# CUDA directory contains bin/ and lib/ directories that we need.
CUDA_DIR := /usr/local/cuda
# On Ubuntu 14.04, if cuda tools are installed via
# "sudo apt-get install nvidia-cuda-toolkit" then use this instead:
# CUDA_DIR := /usr

# CUDA architecture setting: going with all of them.
# For CUDA < 6.0, comment the *_50 lines for compatibility.
CUDA_ARCH := -gencode arch=compute_35,code=sm_35 \
		-gencode arch=compute_50,code=sm_50 \
		-gencode arch=compute_50,code=compute_50

# -gencode arch=compute_20,code=sm_20 \
#		-gencode arch=compute_20,code=sm_21 \
#		-gencode arch=compute_30,code=sm_30 \

# BLAS choice:
# atlas for ATLAS (default)
# mkl for MKL
# open for OpenBlas
BLAS := open
# Custom (MKL/ATLAS/OpenBLAS) include and lib directories.
# Leave commented to accept the defaults for your choice of BLAS
# (which should work)!
BLAS_INCLUDE := /opt/OpenBLAS/include
BLAS_LIB := /opt/OpenBLAS/lib

# This is required only if you will compile the matlab interface.
# MATLAB directory should contain the mex binary in /bin.
MATLAB_DIR := /opt/matlab/r2014a

# NOTE: this is required only if you will compile the python interface.
# We need to be able to find Python.h and numpy/arrayobject.h.
# PYTHON_INCLUDE := /usr/include/python2.7 \
# 		/usr/lib/python2.7/dist-packages/numpy/core/include
# Anaconda Python distribution is quite popular. Include path:
# Verify anaconda location, sometimes it's in root.
ANACONDA_HOME := $(HOME)/anaconda
PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
		$(ANACONDA_HOME)/include/python2.7 \
		$(ANACONDA_HOME)/lib/python2.7/site-packages/numpy/core/include \

# We need to be able to find libpythonX.X.so or .dylib.
PYTHON_LIB := /usr/lib
# PYTHON_LIB := $(ANACONDA_HOME)/lib

# Uncomment to support layers written in Python (will link against Python libs)
# This will require an additional dependency boost_regex provided by boost.
WITH_PYTHON_LAYER := 1

# Whatever else you find you need goes here.
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib

# Uncomment to use `pkg-config` to specify OpenCV library paths.
# (Usually not necessary -- OpenCV libraries are normally installed in one of the above $LIBRARY_DIRS.)
# USE_PKG_CONFIG := 1

BUILD_DIR := build
DISTRIBUTE_DIR := distribute

# Uncomment for debugging. Does not work on OSX due to https://github.com/BVLC/caffe/issues/171
# DEBUG := 1

# The ID of the GPU that 'make runtest' will use to run unit tests.
TEST_GPUID := 0

# enable pretty build (comment to see full commands)
Q ?= @
```

