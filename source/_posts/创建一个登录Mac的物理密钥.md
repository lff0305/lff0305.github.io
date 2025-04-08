---
title: 创建一个登录Mac的物理密钥
date: 2025-04-08 09:56:01
tags: [pico,mac]
---

买了几个树莓派的小型化版本RP2350，30块钱一个，发现它挺有意思， 刷入不同的固件可以做不同的事，包括
- 做FIDO2物理密钥登录各种服务来实现无密码登录；
- 做GPG的物理存储实现密钥和使用分离；
- 做Mac的登录钥匙实现无密码登录Mac账号
下面是Mac物理密钥的生成过程
## 编译固件
因为要模拟有版权的内容， 所以固件要自己编译。
## 安装依赖项
主要的依赖项有：
- ARM tools (arm-gnu-toolchain), 官网下载解压即可
- Pico SDK, Github下载解压即可
- CMake
- GNU Make
- GCC tools
## 下载Pico Keys GPG固件
网址是`https://github.com/polhenarejos/pico-openpgp`， 下载解压到某目录即可。
## 运行cmake，生成Makefile
按照pico-opengpg的文档，运行
```shell
cd <解压目录>
git submodule update --init --recursive
mkdir build
cd build
export PICO_SDK_PATH=<上面的SDK解压路径>
export PICO_TOOLCHAIN_PATH=<上面的ARM tools解压路径>
cmake .. -DPICO_BOARD=waveshare_rp2350_one \
         -DPICO_PLATFORM=rp2350 \
         -DVIDPID=Yubikey5
```

**_注意:_** 因为要使用Yubikey的工具所以要把USB硬件ID通过VIDPID设置成Yubikey5的ID
如果没有问题就

## 修改默认的Product Name
```
cd <解压目录>/pico-keys-sdk/src/usb
vi usb_descriptors.c
```
把`String Descriptiors`里面的`1`和`2`改成Yubikey的名字
```c
//--------------------------------------------------------------------+
// String Descriptors
//--------------------------------------------------------------------+

// array of pointer to string descriptors
char const *string_desc_arr [] = {
    (const char[]) { 0x09, 0x04 }, // 0: is supported language is English (0x0409)
    "YubiCo",                     // 1: Manufacturer
    "Yubikey",                       // 2: Product
    "11223344",                      // 3: Serials, should use chip ID
    "Config"               // 4: Vendor Interface
#ifdef USB_ITF_HID
    , "HID Interface"
    , "HID Keyboard Interface"
#endif
#ifdef USB_ITF_CCID
    , "CCID OTP FIDO Interface"
    , "WebCCID Interface"
#endif
};
```

## 编译

```
cd <解压目录>/build
make
```
如果没有问题会在当前目录下生成`pico-opengpg.ft2`文件。
## 刷入固件
按住`BOOT`键， 将RP2350插入USB接口。会自动识别成一个移动存储。把上一步生成的ft2文件拷入即可。等待几秒钟当LED闪烁的时候刷机完毕。
## 下载安装ykman cli
在https://developers.yubico.com/yubikey-manager/Releases/下载ykman cli工具。
我下载解压后是
```
./ykman --version
YubiKey Manager (ykman) version: 5.6.1
```
## 生成MacOS需要的Key和证书
重新插入RP2350（正常插入，不需要按住BOOT），然后运行下面的命令生成Key和证书：
```
ykman piv keys generate 9a key9a.pub
ykman piv certificates generate 9a -s "CN=MacOS login"
ykman piv keys generate 9d key9d.pub
ykman piv certificates generate 9d -s "CN=encryption"
```

**_注意:_** 默认的PIN是`123456` Admin Pin是`12345678`。

## 测试

将Key重新插拔（不需要按住BOOT）。如果没有问题Mac就会弹出对话框

![](/images/mac2.png)

![](/images/mac1.png)

点击`配对`即可。如果没问题再次登录的时候就可以直接插入Key来实现免密码登录。

## 参考

- https://support.yubico.com/hc/en-us/articles/360016649059-Using-your-YubiKey-as-a-smart-card-in-macOS
- https://support.apple.com/zh-cn/guide/deployment/depc47f60521/web (PIV预置一节）
