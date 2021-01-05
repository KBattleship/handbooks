---
title: "2.Docker安装"
date: 2020-08-22T15:36:27+08:00
lastmod: 2020-08-22T15:36:27+08:00
keywords: ['Docker']
description: ""
tags: ['docker','k8s','容器']
categories: ['K8s']
author: ""
---
# Docker安装

+ **存储库安装**
    1. 安装`yum-config-manager`所需依赖包
        
        ```shell
        $~:sudo yum install -y yum-utils \
            device-mapper-persistent-data \
            lvm2
        ```
    2. 通过`yum-config-manager`添加存储库
    
        ```shell
        $~:sudo yum-config-manager \
            --add-repo \
            https://download.docker.com/linux/centos/docker-ce.repo
        ```
    3. 列出存储库中排序后可用的全部版本

        ```shell
        yum list docker-ce --showduplicates | sort -r
        ```
    4. 进行安装
    
        ```shell
        # 指定版本号安装
        sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
        # 安装最新版本（不指定版本号默认为最新）
        sudo yum install docker-ce docker-ce-cli containerd.io
        ```
    5. 安装docker-compose
    
        ```shell
        # 下载二进制可执行文件，并保存在指定路径
        sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
        # 修改文件权限
        sudo chmod +x /usr/local/bin/docker-compose
        # 创建软链到全局可执行路径
        sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
        ```
+ **软件包安装** 