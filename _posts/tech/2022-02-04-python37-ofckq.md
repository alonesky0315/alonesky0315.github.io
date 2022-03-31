---
layout: post
title: Python3.7 老照片修复算法
category: Python
keywords: 
tags: 
description: 
---

> [https://github.com](https://github.com) 可更换为 [https://github.com.cnpmjs.org](https://github.com.cnpmjs.org) 或 [https://hub.fastgit.org](https://hub.fastgit.org) 镜像
>
> github加速参考
>[https://mp.weixin.qq.com/s/L1Xz9lXoibCsiJpaShohpQ](https://mp.weixin.qq.com/s/L1Xz9lXoibCsiJpaShohpQ)

安装Python3.7 

下载地址：[https://www.python.org/downloads](https://www.python.org/downloads)
一、修复照片划痕，清晰度
1.克隆项目地址
```shell script
git clone https://github.com/microsoft/Bringing-Old-Photos-Back-to-Life
```
2.克隆BatchNorm PyTorch库到脸部和全局
```
cd Face_Enhancement/models/networks/
git clone https://github.com/vacancy/Synchronized-BatchNorm-PyTorch
cp -rf Synchronized-BatchNorm-PyTorch/sync_batchnorm .
cd ../../../

cd Global/detection_models
git clone https://github.com/vacancy/Synchronized-BatchNorm-PyTorch
cp -rf Synchronized-BatchNorm-PyTorch/sync_batchnorm .
cd ../../

```
3.下载landmark detection预训练模型
```
cd Face_Detection/
wget http://dlib.net/files/shape_predictor_68_face_landmarks.dat.bz2
bzip2 -d shape_predictor_68_face_landmarks.dat.bz2
cd ../
```
4.下载预训练库
```
cd Face_Enhancement/
wget https://github.com/microsoft/Bringing-Old-Photos-Back-to-Life/releases/download/v1.0/face_checkpoints.zip
unzip face_checkpoints.zip
cd ../
cd Global/
wget https://github.com/microsoft/Bringing-Old-Photos-Back-to-Life/releases/download/v1.0/global_checkpoints.zip
unzip global_checkpoints.zip
cd ../
```
5.安装依赖包
```
pip install -r requirements.txt
```
> 备注
>
> dlib依赖包失败可使用以下依赖包[dlib-19.17.99-cp37-cp37m-win_amd64.whl](dlib-19.17.99-cp37-cp37m-win_amd64.whl)
> 
> 安装本地依赖包
>
> pip install dlib-19.17.99-cp37-cp37m-win_amd64.whl
>
> 安装指定依赖包
>
> pip install dlib -i https://pypi.doubanio.com/simple
> 
> 
7.测试
```
// 没有划痕的图像：
python run.py --input_folder ./input --output_folder ./output --GPU -1
// 有划痕图像：
python run.py --input_folder ./input --output_folder ./output --GPU -1  --with_scratch
// 有划痕的高分辨率图像：
python run.py --input_folder ./input --output_folder ./output --GPU -1  --with_scratch --HR
```
二、照片上色
1.克隆项目地址
```
git clone https://github.com/jantic/DeOldify
```
2.下载训练模型

下载Artistic [https://data.deepai.org/deoldify/ColorizeArtistic_gen.pth](https://data.deepai.org/deoldify/ColorizeArtistic_gen.pth)

下载Stable(须科学上网) [https://www.dropbox.com/s/usf7uifrctqw9rl/ColorizeStable_gen.pth?dl=0](https://www.dropbox.com/s/usf7uifrctqw9rl/ColorizeStable_gen.pth?dl=0)

下载Video [https://data.deepai.org/deoldify/ColorizeVideo_gen.pth](https://data.deepai.org/deoldify/ColorizeVideo_gen.pth)

    | deoldify
      | models
        | ColorizeVideo_gen.pth (艺术)
        | ColorizeStable_gen.pth (正式)
        | ColorizeArtistic_gen (视频)

3.安装依赖包
```
pip install -r requirements.txt
```
> 备注：Bottleneck依赖包下载失败可使用附件地址下载
>
4.启动jupyterlab服务
```
jupyter lab
```
5.创建文件run.ipynb
```
#NOTE:  This must be the first call in order to work properly!
from deoldify import device
from deoldify.device_id import DeviceId
#choices:  CPU, GPU0...GPU7
device.set(device=DeviceId.CPU)

import torch

if not torch.cuda.is_available():
    print('GPU not available.')

import fastai
from deoldify.visualize import *
import warnings
warnings.filterwarnings("ignore", category=UserWarning, message=".*?Your .*? set is empty.*?")

colorizer = get_image_colorizer(artistic=True)

colorizer.plot_transformed_image("test_images/1.png", render_factor=10, compare=True)

```

附件下载：

源码百度网盘下载：

[https://pan.baidu.com/s/1RBFoC2t-48zJ9jARijZ7bA?pwd=22wr](https://pan.baidu.com/s/1RBFoC2t-48zJ9jARijZ7bA?pwd=22wr)

dlib依赖包下载：

[https://file.alonesky.com/file/python/dlib-19.17.99-cp37-cp37m-win_amd64.whl](https://file.alonesky.com/file/python/dlib-19.17.99-cp37-cp37m-win_amd64.whl)

Bottleneck依赖包下载：

[https://file.alonesky.com/file/python/Bottleneck-1.3.2-cp37-cp37m-win_amd64.whl](https://file.alonesky.com/file/python/Bottleneck-1.3.2-cp37-cp37m-win_amd64.whl)

参考网址：

[https://mp.weixin.qq.com/s/sTaj2ZtoWoIT0ViR3onEpw](https://mp.weixin.qq.com/s/sTaj2ZtoWoIT0ViR3onEpw) 

[https://github.com.cnpmjs.org/microsoft/Bringing-Old-Photos-Back-to-Life](https://github.com.cnpmjs.org/microsoft/Bringing-Old-Photos-Back-to-Life)