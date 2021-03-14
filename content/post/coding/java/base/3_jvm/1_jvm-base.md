---
title: "[ JVM ] 1.关于JVM命令"
date: 2020-12-27T21:10:47+08:00
lastmod: 2020-12-27T21:10:47+08:00
keywords: ['jvm']
description: ""
tags: ['java','jvm','command']
categories: ['Java']
author: ""
---
# 关于JVM命令

## 一、命令列表
1. [jps](#1-jps---jvm进程状态) 
2. [jmap](#2-jmap---jvm堆内存分析工具)
3. [jhat](#3-jhat---用于分析jmap所生成的dump文件)
4. [jstat](#4-jstat---jvm统计监控工具)
5. [jstack](#5-jstack---jvm栈查看工具)
6. [jcmd](#6-jcmd---jvm命令行调试工具)
7. [jinfo](#7-jinfo---jvm信息查看工具)

## 二、命令使用

### **1. jps - JVM进程状态**

+ 用法
    ```shell
    $ jps -help
    usage: jps [-help]
        jps [-q] [-mlvV] [<hostid>]

    Definitions:
        <hostid>:      <hostname>[:<port>]

        # 若不指定<hostid>,则表示当前默认主机或服务器

        # -q  仅输出当前进程号

        # -m  输出JVM启动时传递给main()的参数
        # -l  输出主类名称；如果进程是jar，将输出jar路径
        # -v  输出传递给JVM的参数
        # -V  仅输出本地JVM标识符
    ```

+ 实例讲解
    ```shell
    $ jps -ml
    33270 org.jetbrains.idea.maven.server.RemoteMavenServer36

    $ jps -v
    33270 RemoteMavenServer36 -Djava.awt.headless=true -Dmaven.defaultProjectBuilder.disableGlobalModelCache=true -Didea.maven.embedder.version=3.6.3 -Xmx768m -Dmaven.ext.class.path=/Applications/IntelliJ IDEA.app/Contents/plugins/maven/lib/maven-event-listener.jar -Dfile.encoding=UTF-8
    ```

### **2. jmap - JVM堆内存分析工具**
> 1.用于监控生成**堆**转储快照(heapdump或dump)；

> 2.用于查询`finalize`执行队列、Java堆和永久代的详细信息：当前使用率、当前使用什么类型垃圾回收器；

> 3.也可在启动时增加JVM参数 `-XX:+HeapDumpOnOutOfMemoryError`，在发生OOM时自动生成dump文件。

+ 用法
    ```shell
    $ jmap -h
    Usage:
        jmap [option] <pid>
            (to connect to running process)
        jmap [option] <executable <core>
            (to connect to a core file)
        jmap [option] [server_id@]<remote server IP or hostname>
            (to connect to remote debug server)

    where <option> is one of:
        <none>               to print same info as Solaris pmap
        -heap                to print java heap summary
        -histo[:live]        to print histogram of java object heap; if the "live"
                            suboption is specified, only count live objects
        -clstats             to print class loader statistics
        -finalizerinfo       to print information on objects awaiting finalization
        -dump:<dump-options> to dump java heap in hprof binary format
                            dump-options:
                            live         dump only live objects; if not specified,
                                            all objects in the heap are dumped.
                            format=b     binary format
                            file=<file>  dump heap to <file>
                            Example: jmap -dump:live,format=b,file=heap.bin <pid>
        -F                   force. Use with -dump:<dump-options> <pid> or -histo
                            to force a heap dump or histogram when <pid> does not
                            respond. The "live" suboption is not supported
                            in this mode.
        -h | -help           to print this help message
        -J<flag>             to pass <flag> directly to the runtime system
    ```

    + **jmap** *[option]* \<pid>
      + *none*    --  打印共享参数对象，含有共享对象起始地址、映射大小以及共享对象文件的路径全称。
      + *heap*    --  展示\<pid>整体堆信息，含有概要信息、GC所使用的的算法、Heap配置、wise heap使用情况。
      + *histo*   --  打印每个class的实例数目、内存占用、类全名信息(VM内部类都会以`*`作为前缀)。
      + *histo:live*  --   仅打印存活的对象数量
      + *clstats*     --   全称叫做class loader statistics，用输出类加载有关的统计信息
      + *finalizerinfo*   --  列出准备finalizerinfo对象信息
      + *dump:*[dump-options]
        + live  --  存活对象
        + file=\<filename>  --  dump到指定\<filename>文件中
        + format=b  --  以二进制形式
      + *F*  --   强制
      + *J*\<flag>

+ 实例讲解
  + `jmap heap <pid>`
    ```shell

    ```
  +
### **3. jhat - 用于分析jmap所生成的dump文件**
```shell
# 1.用法

------
# 2.实例
```
### **4. jstat - JVM统计监控工具**
```shell
# 1.用法

------
# 2.实例
```
### **5. jstack - JVM栈查看工具**
```shell
# 1.用法

------
# 2.实例
```
### **6. jcmd - JVM命令行调试工具**
```shell
# 1.用法

------
# 2.实例
```
### **7. jinfo - JVM信息查看工具**
```shell
# 1.用法

------
# 2.实例
```