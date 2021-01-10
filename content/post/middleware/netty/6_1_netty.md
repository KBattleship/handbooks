---
title: "[ Netty ] 1.Netty基础"
date: 2021-01-05T10:36:27+08:00
lastmod: 2021-01-05T10:36:27+08:00
keywords: ['netty']
description: ""
tags: ['java','netty','io']
categories: ['Java']
author: ""
---
# Netty基础
## 从一答一问开始Netty
### 1. 为什么用Netty？
  ```shell
  a. Netty是一个基于JDK NIO的Client/Server架构的框架。可以快速进行网络开发;
  b. 相比JDK NIO，极大简化TCP、UDP套接字服务器网络变成，并且性能、安全性出色;
  c. 支持多种协议：FTP、SMTP、HTTP以及各种二进制和基于文本的传统协议;
  d. 统一的API、支持多种传输类型(阻塞、非阻塞I/O);
  e. 简单且强大的线程模型;
  f. 自带编解码器解决粘包、拆包问题;
  g. 自带各种协议栈;
  h. 安全性不错，支持完整的SSL/TLS及StartTLS协议;
  i. 相比JDK NIO，API具有更高吞吐量、更低延迟、更低资源消耗和更少的内存复制;
  j. 成熟项目很多：Dubbo、RocketMQ。
  ```
### 2. Netty应用场景
  ```shell
  a. RPC框架的网络通信工具
  b. 自实现HTTP服务器
  c. 实现即时通信系统
  d. 实现消息推送系统
  ```
### 3. Netty核心组件
   + **Channel**
     
     对网络操作的抽象类。除I/O基本操作之外，支持`bind()`,`connect()`,`read()`,`write()`操作。

   + **EventLoop**

     EventLoop是Netty的核心抽象，用于处理连接在生命周期中所发生的事件。
     主要作用：负责监听网络事件并调用事件处理器进行相关的I/O操作。

     **`EventLoop`** 负责处理注册到其上的 **`Channel`** 处理I/O操作，两者配合参与I/O操作。

   + **ChannelFuture**

     Netty是`异步非阻塞`，所有的I/O操作都是异步。所以不可以立即得到操作结果。但是

     （1）通过 **`ChannelFuture`** 接口的 **`addListener()`** 注册一个 **`ChannelFutureListener`** 对象，操作执行成功或失败后，监听会自动触发返回结果；

     （2）也可以通过 **`ChannelFuture`** 的 **`channel()`** 获取关联的 **`Channel`** 对象；

     （3) 也可以通过 **`ChannelFuture`** 的 **`sync()`** 进行让异步的操作变成同步。
   + **ChannelHandler**与**ChannelPipeline**

      1. **ChannelHandler** 是消息的具体处理器。他负责处理读写操作、客户端连接等事情。
      
      2. **ChannelPipeline** 为 **ChannelHandler** 链提供了一个容器并定义了用于沿着链传播入站和出站事件流的 API 。当 **Channel** 被创建时，它会被自动地分配到它专属的 **ChannelPipeline** 。

      3. 我们可以在 ChannelPipeline 上通过 addLast() 方法添加一个或者多个ChannelHandler ，因为一个数据或者事件可能会被多个 Handler 处理。当一个 ChannelHandler 处理完之后就将数据交给下一个 ChannelHandler 。
### 4. Netty线程模型
    
  1. 单线程模型

      一个线程需要执行处理所有的 accept、read、decode、process、encode、send 事件。对于高负载、高并发，并且对性能要求比较高的场景不适用。
        ```java 
        //1.eventGroup既用于处理客户端连接，又负责具体的处理。
          EventLoopGroup eventGroup = new NioEventLoopGroup(1);
          //2.创建服务端启动引导/辅助类：ServerBootstrap
          ServerBootstrap b = new ServerBootstrap();
          boobtstrap.group(eventGroup, eventGroup)
          //......
        ```
  2. 多线程模型

        一个 Acceptor 线程只负责监听客户端的连接，一个 NIO 线程池负责具体处理
        满足绝大部分应用场景，并发连接量不大的时候没啥问题，但是遇到并发连接大的时候就可能会出现问题，成为性能瓶颈。
        ```java
        // 1.bossGroup 用于接收连接，workerGroup 用于具体的处理
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
          //2.创建服务端启动引导/辅助类：ServerBootstrap
          ServerBootstrap b = new ServerBootstrap();
          //3.给引导类配置两大线程组,确定了线程模型
          b.group(bossGroup, workerGroup)
            //......
        ```

  3. 主从多线程模型
      
        从一个 主线程 NIO 线程池中选择一个线程作为 Acceptor 线程，绑定监听端口，接收客户端连接的连接，其他线程负责后续的接入认证等工作。连接建立完成后，Sub NIO 线程池负责具体处理 I/O 读写。如果多线程模型无法满足你的需求的时候，可以考虑使用主从多线程模型 。
        ```java
        // 1.bossGroup 用于接收连接，workerGroup 用于具体的处理
        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
          //2.创建服务端启动引导/辅助类：ServerBootstrap
          ServerBootstrap b = new ServerBootstrap();
          //3.给引导类配置两大线程组,确定了线程模型
          b.group(bossGroup, workerGroup)
            //.....
        ``` 

### 5. EventLoopGroup是什么？与EventLoop关系。

  1. `EventLoop` 的主要作用实际就是负责监听网络事件并调用事件处理器进行相关 I/O 操作的处理。
  2. `EventLoopGroup` 包含多个 `EventLoop`（每一个 `EventLoop` 通常内部包含一个线程）。
  并且 EventLoop 处理的 I/O 事件都将在它专有的 Thread 上被处理，即 Thread 和 EventLoop 属于 1 : 1 的关系，从而保证线程安全。

  3. `BossEventLoopGroup` `用于接收连接，WorkerEventLoopGroup` 用于具体的处理（消息的读写以及其他逻辑处理）

  4. 当客户端通过 **connect()** 连接服务端时，`BossEventLoopGroup` 处理客户端连接请求。当客户端处理完成后，会将这个连接提交给 `WorkerEventLoopGroup` 来处理，然后 `WorkerEventLoopGroup` 负责处理其 IO 相关操作。

### 6. Netty中TCP粘包、拆包

  **TCP 粘包/拆包** 就是基于 TCP 发送数据的时候，出现了多个字符串“粘”在了一起或者一个字符串被“拆”开的问题。

  **解决办法：**
 + 使用 Netty 自带的解码器
   1. **LineBasedFrameDecoder** : 发送端发送数据包的时候，每个数据包之间以换行符作为分隔，**LineBasedFrameDecoder** 的工作原理是它依次遍历 **ByteBuf** 中的可读字节，判断是否有换行符，然后进行相应的截取。 
   2. **DelimiterBasedFrameDecoder** : 可以自定义分隔符解码器，**LineBasedFrameDecoder** 实际上是一种特殊的 **DelimiterBasedFrameDecoder** 解码器。
   3. **FixedLengthFrameDecoder**: 固定长度解码器，它能够按照指定的长度对消息进行相应的拆包。
   4. **LengthFieldBasedFrameDecoder** + **LengthFieldPrepender** 配合使用: 处理粘拆包的主要思想是在生成的数据包中添加一个长度字段，用于记录当前数据包的长度。(**分为有头部的拆包与粘包、长度字段在前且有头部的拆包与粘包、多扩展头部的拆包与粘包。**)
      + `LengthFieldBasedFrameDecoder`会按照参数指定的包长度偏移量数据对接收到的数据进行解码，从而得到目标消息体数据 
      + `LengthFieldPrepender`则会在响应的数据前面添加指定的字节数据，这个字节数据中保存了当前消息体的整体字节数据长度
 + 自定义序列化编解码器
   + 通过继承`LengthFieldBasedFrameDecoder`和`LengthFieldPrepender`来实现粘包和拆包的处理

### 7. Netty长连接、心跳机制

  在 TCP 保持长连接的过程中，可能会出现断网等网络异常出现，异常发生的时候， client 与 server 之间如果没有交互的话，它们是无法发现对方已经掉线的。为了解决这个问题, 我们就需要引入 **心跳机制** 。

  + **心跳机制的工作原理是:** 在 client 与 server 之间在一定时间内没有数据交互时, 即处于 idle 状态时, 客户端或服务器就会发送一个特殊的数据包给对方, 当接收方收到这个数据报文后, 也立即发送一个特殊的数据报文, 回应发送方, 此即一个 PING-PONG 交互。所以, 当某一端收到心跳消息后, 就知道了对方仍然在线, 这就确保 TCP 连接的有效性.

  + **Netty中的心跳机制实现：** TCP 实际上自带的就有长连接选项，本身是也有心跳包机制，也就是 TCP 的选项：SO_KEEPALIVE。但是，TCP 协议层面的长连接灵活性不够。所以，一般情况下我们都是在应用层协议上实现自定义心跳机制的，也就是在 Netty 层面通过编码实现。通过 Netty 实现心跳机制的话，核心类是 IdleStateHandler
### 8. Netty零拷贝
   
   在 OS 层面上的 Zero-copy 通常指避免在 `用户态(User-space)` 与 `内核态(Kernel-space)` 之间来回拷贝数据。而在 Netty 层面，零拷贝主要体现在对于数据操作的优化。

   Netty 中的零拷贝体现在以下几个方面：
  1. 使用 Netty 提供的 `CompositeByteBuf` 类， 可以将多个ByteBuffer 合并为一个逻辑上的 ByteBuffer， **避免了各个 ByteBuf 之间的拷贝**。
  2. ByteBuffer 支持 slice 操作， 因此可以将 ByteBuf 分解为多个共享同一个存储区域的 ByteBuf， **避免了内存的拷贝**。
  3. 通过 FileRegion 包装的FileChannel.tranferTo 实现文件传输， 可以直接将文件缓冲区的数据发送到目标 Channel， **避免了传统通过循环 write 方式导致的内存拷贝问题**。