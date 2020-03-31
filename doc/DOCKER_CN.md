# Docker编译环境准备

(简体中文|[English](./DOCKER.md))

## 环境要求

+ 开发机上已安装Docker。
+ 编译GPU版本需要安装nvidia-docker。

## Dockerfile文件

[CPU版本Dockerfile](../tools/Dockerfile)

[GPU版本Dockerfile](../tools/Dockerfile.gpu)

## 使用方法

### 构建Docker镜像

建立新目录，复制Dockerfile内容到该目录下Dockerfile文件。

执行

```bash
docker build -t serving_compile:cpu .
```

或者

```bash
docker build -t serving_compile:cuda9 .
```

## 进入Docker

CPU版本请执行

```bash
docker run -it serving_compile:cpu bash
```

GPU版本请执行

```bash
docker run -it --runtime=nvidia -it serving_compile:cuda9 bash
```

## Docker编译出的可执行文件支持的环境列表

经过验证的环境列表如下：

| CPU Docker编译出的可执行文件支持的系统环境 |
| -------------------------- |
| Centos6                    |
| Centos7                    |
| Ubuntu16.04                |
| Ubuntu18.04               |



| GPU Docker编译出的可执行文件支持的系统环境 |
| ---------------------------------- |
| Centos6_cuda9_cudnn7                       |
| Centos7_cuda9_cudnn7                  |
| Ubuntu16.04_cuda9_cudnn7                       |
| Ubuntu16.04_cuda10_cudnn7                  |



**备注：** 
+ 若执行预编译版本出现找不到libcrypto.so.10、libssl.so.10的情况，可以将Docker环境中的/usr/lib64/libssl.so.10与/usr/lib64/libcrypto.so.10复制到可执行文件所在目录。
+ CPU预编译版本仅可在CPU机器上执行，GPU预编译版本仅可在GPU机器上执行。
