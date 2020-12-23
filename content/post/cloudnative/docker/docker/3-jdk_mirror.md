---
title: "3.Docker之jdk1.8最简镜像构建"
date: 2020-08-22T15:36:27+08:00
lastmod: 2020-08-22T15:36:27+08:00
keywords: ['Docker']
description: ""
tags: ['docker','k8s','容器']
categories: ['K8s']
author: ""
---
# Docker之jdk1.8最简镜像构建
## 1.准备JRE
在[Java下载网站](https://www.java.com/en/download/manual.jsp)下载JRE。
`Tips:此JRE为Oracle作品，而非Openjdk`
## 2.精简JRE中无关文件
```shell
# 进入已经下载jre压缩包的路径,执行解压
tar xzvf ~/Downloads/jre-8u241-linux-x64.tar.gz&&cd jre1.8.0_241
# 删除说明、其他文档
rm -rf COPYRIGHT LICENSE README \
THIRDPARTYLICENSEREADME-JAVAFX.txt \
THIRDPARTYLICENSEREADME.txt \
Welcome.html

# 删除非必要依赖文件
rm -rf     lib/plugin.jar \
           lib/ext/jfxrt.jar \
           bin/javaws \
           lib/javaws.jar \
           lib/desktop \
           plugin \
           lib/deploy* \
           lib/*javafx* \
           lib/*jfx* \
           lib/amd64/libdecora_sse.so \
           lib/amd64/libprism_*.so \
           lib/amd64/libfxplugins.so \
           lib/amd64/libglass.so \
           lib/amd64/libgstreamer-lite.so \
           lib/amd64/libjavafx*.so \
           lib/amd64/libjfx*.so

# 移除完成后文件大小共111M，然后进行压缩;压缩后大小为44M
tar czvf jre8.tar.gz *
```
## 3.编写DockerFile
```DockerFile
FROM docker.io/jeanblanchard/alpine-glibc

MAINTAINER cheneyin xy410257@163.com

ADD jre8.tar.gz /usr/local/java/jdk8/
ENV JAVA_HOME /usr/local/java/jdk8
ENV PATH ${PATH}:${JAVA_HOME}/bin

WORKDIR /opt
```
`Tips:由于Java依赖于glibc，基础镜像选择alpine-glibc并非alpine`

## 4.构建打包
```shell
docker build -t touch_star/java8:1.0 .
```
## 5.测试运行
```shell
docker run -it touch_star/java8:1.0

####    Print   ####
/opt # java -version
java version "1.8.0_241"
Java(TM) SE Runtime Environment (build 1.8.0_241-b07)
Java HotSpot(TM) 64-Bit Server VM (build 25.241-b07, mixed mode)
/opt # 
```