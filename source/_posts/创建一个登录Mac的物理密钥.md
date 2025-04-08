---
title: 创建一个登录Mac的物理密钥
date: 2025-04-08 09:56:01
tags: pico mac
---

买了几个树莓派的小型化版本RP2350，30块钱一个，发现它挺有意思， 刷入不同的固件可以做不同的事，包括
- 做FIDO2物理密钥登录各种服务来实现无密码登录；
- 做GPG的物理存储实现密钥和使用分离；
- 做Mac的登录钥匙实现无密码登录Mac账号

下面是Mac物理密钥的生成过程

# 编译固件
因为要模拟有版权的内容， 所以固件要自己编译。
# 安装依赖项
主要的依赖项有：
- ARM tools (arm-gnu-toolchain), 官网下载解压即可
- Pico SDK, Github下载解压即可
- CMake
- GNU Make
- GCC tools
# 下载Pico Keys GPG固件
网址是`https://github.com/polhenarejos/pico-openpgp`， 下载解压到某目录即可。
# 运行cmake，生成Makefile
按照pico-opengpg的文档，运行
```
cd <解压目录>
git submodule update --init --recursive
mkdir build
cd build
export PICO_SDK_PATH=<上面的SDK解压路径>
export PICO_TOOLCHAIN_PATH=<上面的ARM tools解压路径>
cmake .. -DPICO_BOARD=waveshare_rp2350_one -DPICO_PLATFORM=rp2350 -DVIDPID=Yubikey5
```
> [!TIP]
> 因为要使用Yubikey的工具所以要把USB硬件ID设置成Yubikey5的ID, 通过VIDPID
Tip
