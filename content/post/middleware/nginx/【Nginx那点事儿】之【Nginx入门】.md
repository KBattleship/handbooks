# 【Nginx那点事儿】之 ①【 Nginx入门】

### 一、Nginx为什么受青睐

​	在介绍***Nginx***具体的安装、配置以及原理之前先聊聊概念常识问题。那就是目前为什么***Nginx***深受青睐？那我们先从***Nginx***是什么开始聊起。

#### 1.Nginx是什么

- **简单介绍**

		 ***Nginx***来自于俄罗斯，是在**RamblerMedia**工作的**Igor Sysoev**使用***C***语言编写而成的跨平台轻量级高性能的*Web*服务器。***Nginx***可以运行在**Linux**、**FreeBSD**、**Solaris**(*Sun*公司的类*Unix OS*)、**MacOS**、以及**Windows**等操作系统。操作系统的不同，也给***Nginx***带来了一些好处，***Nginx***会使用当前操作系统中特有的一些高效**API**来提高自身的性能。

- **Nginx和它的对手们**

  ***Nginx***的对手们有**Apache**、**Lighttpd**、**Tomcat**、**Jetty**、**IIS**，它们同为*Web*服务器：具备*Web*服务器的基本功能；基于*Rest*架构风格，以***统一资源描述符（URI）***或***统一资源定位符（URL）***作为沟通依据，通过*HTTP*为浏览器等*Client*程序提供各种网络服务。

  但是，这些*Web*服务器呢，都多多少少因为各自的定位与发展方向都不尽相同，使得每一款*Web*服务器都各有特色：

  ​	1.**Tomcat、Jetty：**都是面向Java语言设计的。但是它们在性能方面与**Nginx**没有什么可比性，因为这两款服务器都是重量级选手。可能有伙伴会很疑惑，我已经用*Tomcat*跑起服务，同样配置后可以直接访问为什么还要在加层外套**Nginx**，对于这个问题，在后边对这一点进行详细的分析。【】

  ​	2.**IIS：**这位选手呢，来自于微软家族。然后特点大家可能就很清楚了，它只能在Windows OS运行（不过网上也有工具可以把它运行在*LinuxOS*中，但是并不是很完美哦）。可能拉低它颜值的就是**稳定性**与**性能**了，*Windows OS*作为服务器的话，稳定性和部分性能都不能和类*Unix OS*进行媲美，所以呢，在高性能*Web*服务器的场合中，**IIS**可能就要被“淘汰”了。

  ​	3.**Apache：**这是一位压轴级选手，是发展周期最长的，毫无疑问是**世界第一大*Web*服务器**，在2012年遥遥领先其他选手。它毕竟有很多优秀的地方：**稳定**、**开源**、**跨平台**等。但是美中不足的是，它被设计成为了**重量级**、**不支持高并发**的*Web*服务器。如果有数以万计的*HTTP*请求同时访问，服务器就会面临大量内存消耗的问题，操作系统也会跟着收到牵连，毕竟Apache的进程做进程间切换时会给服务器的CPU带来重大压力，同时会伴随着响应效率降低，这致命的一击，导致这位来自“贵族世家”的选手在**高性能Web服务器**的舞台上没有了地位。

  ​	4.**Lighttpd：**与**Nginx**同样是轻量级、高性能的Web服务器。但是它并没有得到国内开发者的钟爱，而是被欧美的开发者们所追捧。

- **恩宠--Nginx**

  **Nginx**的代码也是开源的而且是最自由的***2-clause BSD-like license***许可证。**Nginx**使用的架构是**基于事件驱动**的，能够并发处理百万级别的TCP连接。由于**Nginx**的高度模块化和具有最自由的许可证，让**Nginx**的第三方模块扩展功能更加充实。优秀的设计还带来了极佳的稳定性体验。所以，**Nginx**大量应用于大流量的网站来高效处理大规模高并发连接。种种迹象表明，**Nginx**在性能方面很出色。

#### 2.Nginx的特点

- **更快**

  快主要体现在两方面：①在正常的情况下，单次请求会得到更快的响应；②在数以万计的并发请求中，**Nginx**可以比其他*Web*服务器更快的响应请求。

- **高扩展性**

  **Nginx**的高度模块化决定了其具有高扩展性。它完全是由多个不同功能、不同层次、不同类型以及耦合度极低的模块组合而成。它的模块都是嵌入到二进制文件中执行，使得第三方开发的模块也一样完美支持性能。所以高并发的网站完全可以根据自身项目业务特性定制属于自己的模块。

- **高可靠性**

  这个特点应该是选择*Web*服务器最基本的条件。**Nginx**的稳定性，大家有目共睹。国内多家高流量并发的网站在核心的服务器上大规模使用**Nginx**。官方提供的常用模块是非常稳定的，每一个Worker进程都相对独立，把耦合性降至最低。*master*进程在其中一个*Worker*进程出错时可以快速“拉起”新的*Worker*子进程提供相应的服务。

- **低内存消耗**

  据数据测试，一般情况下，1W个不活跃的*HTTP Keep-Alive*连接在**Nginx**中消耗只有2.5MB的内存。（这也是**Nginx**能够支持高并发连接的基础）

- **单机支持10W+的并发连接**

  由于现在是海量数据时代，高并发无疑成为大家青睐的对象。理论上，**Nginx**支持的并发连接数量取决于内存，10W+的并发连接并没有到极限。但是，能否及时处理更多的并发连接应该取决于项目业务的需求。

- **热部署**

  *master*管理进程和*Worker*进程是相互隔离的，这使得**Nginx**能够彰显热部署的能力。通俗点来说，就是完全可以在*7\*24*h不停止服务正常工作的情况下，可以升级**Nginx**的可执行文件、更新配置选项、更新日志文件等功能操作。

- **最自由的BSD许可协议**

  俗话说**“众人拾柴火焰高”**。也正是BSD许可协议带来的极大优势，为**Nginx**提供更强劲的发展动力。

##### **综上所述，选择*Nginx*的核心理由还是由于它能在支持高并发请求的同时保持高效的服务。**

---

### 二、Nginx的安装

#### 1.源码安装

在正式安装**Nginx**前需要保证服务器主机已经安装有**编译环境*GCC*开发库**之类的环境。

- GCC编译环境工具安装

①*Ubuntu OS*编译环境使用如下命令：

```shell
apt-get install build-essential
apt-get install libtool
```

②CentOS编译环境使用如下命令：

```shell
yum install -y gcc automake autoconf libtool make
yum install -y gcc -c++
```

安装完成编译环境，就可以着手准备**Nginx**所需要的类库**PCRE库**、**zlib库**、**OpenSSL开发库**。

- PCRE库安装

首先介绍一下PCRE库的作用，为**Nginx**的*HTTP*模块提供**解析正则表达式**的基础。这里直接通过下载源码的方式进行编译安装。根据需要的版本在[PCRE源码](https://ftp.pcre.org/pub/pcre/)中选择URL然后用下边命令进行下载

```shell
wget https://ftp.pcre.org/pub/pcre/pcre-8.42.tar.gz
## 现在完成后，对源码包进行解压
tar -xzvf pcre-8.42.tar.gz
## 解压完成进入pcre-8.42目录
cd pcre-8.42
## 执行配置
./configure
## 进行编译并安装
make 
make install
```

- Zlib库安装

Zlib库主要是针对HTTP包的内容**做gzip格式的压缩**。例如，**Nginx**的配置*nginx.conf*文件中配置***gzip on***。[Zlib-1.2.11下载](http://zlib.net/zlib-1.2.11.tar.gz)

```shell
## 使用Wget命令下载源码
wget http://zlib.net/zlib-1.2.11.tar.gz
## 进行解压
tar -xzvf zlib-1.2.11.tar.gz
## 进入zlib目录进行配置编译安装操作
cd zlib-1.2.11
./configure
make 
make install
```

- Openssl安装

如果对版本没有特殊要求，OpenSSL我们采用命令安装

```shell
## CentOS 安装命令
yum install openssl openssl-devel

## Ubuntu/Debian 安装命令
sudo apt-get install openssl        
sudo apt-get install libssl-devel  

```

- **Nginx安装**

首先去[**Nginx**官网](http://nginx.org/en/download.html)下载合适版本的源码。同时我们可以直接在服务器使用wget命令进行下载。

```shell
wget http://nginx.org/download/nginx-1.14.0.tar.gz
## 解压Nginx源码
tar -xzvf nginx-1.14.0.tar.gz
cd nginx-1.14.0

## 进行配置项的配置:(以下只是选择了编译Nginx源码时的部分配置选项)
	# --sbin-path: 指定了可执行文件的放置路径,默认路径在<prefix>/sbin/nginx
	# --conf-path: 指定了配置选项文件的放置路径,默认路径在<prefix>/conf/nginx.conf
	# --pid-path: 指定了pid文件的放置路径,默认路径在<prefix>/logs>nginx.pid
	# --with-http_ssl_module:安装http_ssl_module，使得Nginx支持SSL协议，保证HTTPS服务。
	# --with-pcre: 指定PCRE库源码的路径，编译Nginx时会进入此路径对源码进行编译。
	# --with-zlib: 指定Zlib库源码的路径，编译Nginx时会进入此路径对源码进行编译。
./configure  \
--sbin-path=/usr/local/nginx/nginx \ 
--conf-path=/usr/local/nginx/nginx.conf \
--pid-path=/usr/local/nginx/nginx.pid \
--with-http_ssl_module \
--with-pcre=/opt/app/openet/oetal1/cheney/pcre-8.42 \
--with-zlib=/opt/app/openet/oetal1/cheney/zlib-1.2.11 

## 配置完成后进行编译安装
make 
make install
```

安装完成之后可以进行简单的测试，进入配置的**${--sbin-path}**路径下启动Nginx。

```shell
## 直接使用默认配置执行nginx可执行文件启动服务
/usr/local/nginx/sbin/nginx
```

在保证服务器主机端口可以访问的情况下，使用浏览器访问主机IP地址，例如***http://127.0.0.1***，可以看到Nginx服务的默认主页。这就说明**Nginx**服务正常启动了。然后可以根据项目具体的业务需求，对**Nginx**进行特殊配置进行详细定制。

---

### Nginx的具体配值问题，下篇文章进行详细介绍。

