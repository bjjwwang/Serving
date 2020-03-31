# How to compile PaddleServing

([简体中文](./COMPILE_CN.md)|English)

## Compilation environment requirements

- os: CentOS 6u3
- gcc: 4.8.2 and later
- go: 1.9.2 and later
- git：2.17.1 and later
- cmake：3.2.2 and later
- python：2.7.2 and later

It is recommended to use Docker to prepare the compilation environment for the Paddle service: [CPU Dockerfile.devel](../tools/Dockerfile.devel), [GPU Dockerfile.gpu.devel](../tools/Dockerfile.gpu.devel)

## Get Code

``` python
git clone https://github.com/PaddlePaddle/Serving
cd Serving && git submodule update --init --recursive
```

## PYTHONROOT Setting

```shell
# for example, the path of python is /usr/bin/python, you can set /usr as PYTHONROOT
export PYTHONROOT=/usr/
```

## Compile Server

### Integrated CPU version paddle inference library

``` shell
mkdir build && cd build
cmake -DPYTHON_INCLUDE_DIR=$PYTHONROOT/include/python2.7/ -DPYTHON_LIBRARIES=$PYTHONROOT/lib/libpython2.7.so -DPYTHON_EXECUTABLE=$PYTHONROOT/bin/python -DSERVER=ON ..
make -j10
```

you can execute `make install` to put targets under directory `./output`, you need to add`-DCMAKE_INSTALL_PREFIX=./output`to specify output path to cmake command shown above.

### Integrated GPU version paddle inference library

``` shell
mkdir build && cd build
cmake -DPYTHON_INCLUDE_DIR=$PYTHONROOT/include/python2.7/ -DPYTHON_LIBRARIES=$PYTHONROOT/lib/libpython2.7.so -DPYTHON_EXECUTABLE=$PYTHONROOT/bin/python -DSERVER=ON -DWITH_GPU=ON ..
make -j10
```

execute `make install` to put targets under directory `./output`

## Compile Client

``` shell
mkdir build && cd build
cmake -DPYTHON_INCLUDE_DIR=$PYTHONROOT/include/python2.7/ -DPYTHON_LIBRARIES=$PYTHONROOT/lib/libpython2.7.so -DPYTHON_EXECUTABLE=$PYTHONROOT/bin/python -DCLIENT=ON ..
make -j10
```

execute `make install` to put targets under directory `./output`

## Compile the App

```bash
mkdir build && cd build
cmake -DPYTHON_INCLUDE_DIR=$PYTHONROOT/include/python2.7/ -DPYTHON_LIBRARIES=$PYTHONROOT/lib/libpython2.7.so -DPYTHON_EXECUTABLE=$PYTHONROOT/bin/python -DAPP=ON ..
make
```

## Install wheel package

Regardless of the client, server or App part, after compiling, install the whl package under `python/dist/`.

## Note

When running the python server, it will check the `SERVING_BIN` environment variable. If you want to use your own compiled binary file, set the environment variable to the path of the corresponding binary file, usually`export SERVING_BIN=${BUILD_DIR}/core/general-server/serving`.


## CMake Option Description

| Compile Options  |                    Description             | Default |
| :--------------: | :----------------------------------------: | :--: |
|     WITH_AVX     | Compile Paddle Serving with AVX intrinsics | OFF  |
|     WITH_MKL     |  Compile Paddle Serving with MKL support   | OFF  |
|     WITH_GPU     |   Compile Paddle Serving with NVIDIA GPU   | OFF  |
|    CUDNN_ROOT    |    Define CuDNN library and header path    |      |
|      CLIENT      |       Compile Paddle Serving Client        | OFF  |
|      SERVER      |       Compile Paddle Serving Server        | OFF  |
|       APP        |     Compile Paddle Serving App package     | OFF  |
| WITH_ELASTIC_CTR |        Compile ELASITC-CTR solution        | OFF  |
|       PACK       |              Compile for whl               | OFF  |

### WITH_GPU Option

Paddle Serving supports prediction on the GPU through the PaddlePaddle inference library. The WITH_GPU option is used to detect basic libraries such as CUDA/CUDNN on the system. If an appropriate version is detected, the GPU Kernel will be compiled when PaddlePaddle is compiled.

To compile the Paddle Serving GPU version on bare metal, you need to install these basic libraries:

- CUDA
- CuDNN
- NCCL2

Note here:

1. The basic library versions such as CUDA/CUDNN installed on the system where Serving is compiled, needs to be compatible with the actual GPU device. For example, the Tesla V100 card requires at least CUDA 9.0. If the version of the basic library such as CUDA used during compilation is too low, the generated GPU code is not compatible with the actual hardware device, which will cause the Serving process to fail to start or serious problems such as coredump.
2. Install the CUDA driver compatible with the actual GPU device on the system running Paddle Serving, and install the basic library compatible with the CUDA/CuDNN version used during compilation. If the version of CUDA/CuDNN installed on the system running Paddle Serving is lower than the version used at compile time, it may cause some cuda function call failures and other problems.


The following is the base library version matching relationship used by the PaddlePaddle release version for reference:

|        |  CUDA   |          CuDNN           | NCCL2  |
| :----: | :-----: | :----------------------: | :----: |
| CUDA 8 | 8.0.61  | CuDNN 7.1.2 for CUDA 8.0 | 2.1.4  |
| CUDA 9 | 9.0.176 | CuDNN 7.3.1 for CUDA 9.0 | 2.2.12 |

### How to make the compiler detect the CuDNN library

Download the corresponding CUDNN version from NVIDIA developer official website and decompressing it, add `-DCUDNN_ROOT` to cmake command, to specify the path of CUDNN.

### How to make the compiler detect the nccl library

After downloading the corresponding version of the nccl2 library from the NVIDIA developer official website and decompressing it, add the following environment variables (take nccl2.1.4 as an example):

```shell
export C_INCLUDE_PATH=/path/to/nccl2/cuda8/nccl_2.1.4-1+cuda8.0_x86_64/include:$C_INCLUDE_PATH
export CPLUS_INCLUDE_PATH=/path/to/nccl2/cuda8/nccl_2.1.4-1+cuda8.0_x86_64/include:$CPLUS_INCLUDE_PATH
export LD_LIBRARY_PATH=/path/to/nccl2/cuda8/nccl_2.1.4-1+cuda8.0_x86_64/lib/:$LD_LIBRARY_PATH
```
