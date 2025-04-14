---
title: 使用Docker来编译pico-fido
date: 2025-04-14 21:38:01
tags: [pico,docker]
---

Dockerfile:
```docker
FROM ubuntu:24.04

RUN apt-get update -y && \
    apt-get install -y git cmake python3 build-essential

RUN apt-get install -y wget xz-utils

RUN mkdir /toolchain && cd toolchain && \
    wget https://developer.arm.com/-/media/Files/downloads/gnu/14.2.rel1/binrel/arm-gnu-toolchain-14.2.rel1-x86_64-arm-none-eabi.tar.xz && \
    xz -d arm-gnu-toolchain-14.2.rel1-x86_64-arm-none-eabi.tar.xz && \
    tar xvf arm-gnu-toolchain-14.2.rel1-x86_64-arm-none-eabi.tar

RUN ls -alh /toolchain/arm-gnu-toolchain-14.2.rel1-x86_64-arm-none-eabi


RUN cd /tmp &&  git clone https://github.com/raspberrypi/pico-sdk.git && \
    cd pico-sdk && \
    git submodule update --init
   
RUN mkdir /src && \
    cd src && \
    git clone https://github.com/polhenarejos/pico-fido.git && cd pico-fido && \
    git submodule update --init --recursive

RUN cd /src/pico-fido && mkdir build && \
    cd build && \
    PICO_TOOLCHAIN_PATH=/toolchain/arm-gnu-toolchain-14.2.rel1-x86_64-arm-none-eabi PICO_SDK_PATH=/tmp/pico-sdk cmake .. -DPICO_BOARD=waveshare_rp2350_one -DVIDPID=Yubikey5 -DPICO_PLATFORM=rp2350 && \
    make

RUN cd /src/pico-fido/build && ls -al
```

使用：
```shell
mkdir dist
docker build . -t pico-fido-build:dev
docker run --rm -u $(id -u):$(id -g) -v ./dist:/dist pico-fido-build:dev sh -c "cp -r /src/pico-fido/build/*.uf2 /dist"
```
