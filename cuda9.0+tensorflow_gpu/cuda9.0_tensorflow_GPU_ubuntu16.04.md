### 在ubuntu16.04上安装GPU版Tensorflow

#### 安装ubuntu16.04

直接在官网下载：https://www.ubuntu.com/download/desktop

或者采用系统盘安装

#### 安装英伟达显卡驱动

在ubuntu16.04中，查看显卡驱动非常简单，在

系统设置->软件更新->附加驱动->切换到最新的NVIDIA驱动即可。应用更改->重启

![01.png](https://github.com/ChaoZeyi/software/blob/master/cuda9.0+tensorflow_gpu/pics/01.png?raw=true)

然后使用命令nvidia-smi查看相应信息

![02.jpg](https://github.com/ChaoZeyi/software/blob/master/cuda9.0+tensorflow_gpu/pics/02.jpg?raw=true)

#### 安装CUDA

在https://developer.nvidia.com/cuda-downloads下载对应版本的cuda:

![03.jpg](https://github.com/ChaoZeyi/software/blob/master/cuda9.0+tensorflow_gpu/pics/03.jpg?raw=true)

按照下面的步骤安装即可

1. `sudo dpkg -i cuda-repo-ubuntu1604-9-0-local_9.0.176-1_amd64.deb`
2. `sudo apt-key add /var/cuda-repo-<version>/7fa2af80.pub`
3. `sudo apt-get update`
4. `sudo apt-get install cuda`

ubuntu16.04的gcc编译器是5.4.0，然而cuda8.0不支持5.0以上的编译器，因此需要降级，把编译器版本降到4.9。

![04.jpg](https://github.com/ChaoZeyi/software/blob/master/cuda9.0+tensorflow_gpu/pics/04.jpg?raw=true)

如果是cuda9.0，则跳过gcc降级的步骤。

#### gcc降级

```
sudo apt-get install gcc -4.9 gcc-5 g++-4.9 g++-5

sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.9 20

sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 10

sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.9 20

sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-5 10

sudo update-alternatives --install /usr/bin/cc cc /usr/bin/gcc 30

sudo update-alternatives --set cc /usr/bin/gcc

sudo update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++ 30

sudo update-alternatives --set c++ /usr/bin/g++
```

安装cuda的最后一步就是修改环境变量：

vim ~/.bashrc

打开文件，并在最后添加一下3行

```
export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:/usr/local/cuda9.0/extras/CUPTI/lib64:$LD_LIBRARY_PATH

export CUDA_HOME=/usr/local/cuda-9.0

export PATH=/usr/local/cuda-9.0/bin:$PATH
```

使用source ~/.bashrc使其生效

安装到这里，cuda应该是已经装好了，接下来进行测试：

进入到/usr/local/cuda9.0/samples 文件夹

```
make -j
```

编译成功后，会生成/usr/local/cuda-8.0/samples/bin/x86_64/linux/release文件夹，里面都是编译好的可执行文件

```
./deviceQuery
```

如果不报错，显示的是一些关于GPU的信息，则说明cuda安装成功。

#### 安装cuDNN

下载对应版本的cuDNN： <https://developer.nvidia.com/cudnn>

cuDNN v5.1 Library for Linux版本

cd到cuda的安装目录执行解压命令：

```
$ cd /usr/local
$ sudo tar -xzvf ~/downloads/cudnn-9.0-linux-x64-v5.1.tgz12
```

这样就完成了cuDNN的安装。

可以通过命令

```
nvcc -V
```

来查看cuda版本

![05.jpg](https://github.com/ChaoZeyi/software/blob/master/cuda9.0+tensorflow_gpu/pics/05.jpg?raw=true)

#### 安装GPU版Tensorflow

python3.5, ubuntu16.04, tensorflow-gpu

> sudo pip3 install --upgrade tensorflow-gpu

安装成功，但进入python3执行import操作时报错

> python3
>
> import tensorflow as tf

报错：

> importError: libcusolver.so.8.0: cannot open shared object file: No such file or directory
>
> Failed to load the native TensorFlow runtime.
>
> See https://www.tensorflow.org/install/install_sources#common_installation_problems

查了各种资料，才发现原来是因为 /usr/local/cuda9.0/lib64/ 下只有 libcusolver.so.9.0 这个文件，而没有libcusolver.so.8.0，搜了很久，基本上确定这是由于Tensorflow官方版本目前不支持CUDA9, 支撑CUDA8的缘故，所以这个pip版本默认找得是CUDA8.0的后缀文件： libcusolver.so.8.0 ，无法识别 libcusolver.so.9.0 这个文件。

[Upgrade to CuDNN 7 and CUDA 9 ](https://github.com/tensorflow/tensorflow/issues/12052)：请求TensorFlow官方版本支持CUDA9和cuDNN7的讨论：Please upgrade TensorFlow to support CUDA 9 and CuDNN 7. Nvidia claims this will provide a 2x performance boost on Pascal GPUs

 [CUDA 9RC + cuDNN7 ](https://github.com/tensorflow/tensorflow/issues/12474)：在Tensorflow中支持CUDA9和cuDNN7的源代码安装方案：This is an unofficial and very not supported patch to make it possible to compile TensorFlow with CUDA9RC and cuDNN 7 or CUDA8 + cuDNN 7.

安装到这里，其实最好的办法是把cuda9.0换为8.0，又因为目前英伟达官网只提供了最新的9.0下载版本，没办法，只能硬着头发用tensorflow的源代码安装。这个百度上很多，步骤很麻烦，就不记录了。

到最后安装成功：

![06.jpg](https://github.com/ChaoZeyi/software/blob/master/cuda9.0+tensorflow_gpu/pics/06.jpg?raw=true)

#### 参考：

http://blog.csdn.net/zhaoyu106/article/details/52793183

http://ju.outofmemory.cn/entry/328609