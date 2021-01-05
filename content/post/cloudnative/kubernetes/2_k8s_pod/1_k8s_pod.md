# 一、了解Pod对象
#### 1.Pod参数定义
```yaml
# 必填,版本号
apiVersion: string
kind: Pod
# 必填,元数据
metadata:
    # 必填,Pod对象的名称(命名规范需要符合RFC 1035规范)
    name: string
    # 必填,Pod对象所属的命名空间,默认值为default
    namespace: string
    # 自定义标签列表(取值类型:List)
    labels: 
      - name: string
    # 自定义标签注解(取值类型:List)
    annotations:
      - name: string
# 必填,Pod对象中容器的详细定义  
spec:
    # 必填,Pod对象容器列表(取值类型:List)
    containers:
        # 必填,容器的名称(需要符合RFC 1035规范)
      - name: string
        # 必填,容器镜像名称
        image: string
        # 获取镜像的策略，默认值为:Always
        # Always: 每次都尝试重新下载镜像
        # Never: 仅使用本地镜像
        # IfNotPresent: 如果本地不存在，就下载镜像
        imagePullPolicy: [Always | Never | IfNotPresent]
        # 容器启动命令列表，若不指定则使用镜像打包时使用的启动命令
        command: [string]
        # 容器的启动命令参数列表
        args: [string]
        # 容器的工作目录
        workingDir: string
        # 挂载到容器内部的存储卷配置(取值类型:List)
        volumeMounts:
            # 引用Pod定义的共享存储卷的名称，需使用镜像volumes[]部分定义的共享卷名称
          - name: string
            # 存储卷在容器内Mount的绝对路径(应少于512个字符)
            mountPath: string
            # 是否只读模式,默认false(读写模式)
            readOnly: boolean
        # 容器需要暴露的端口号(取值类型:List)    
        ports:
            # 端口的名称
          - name: string
            # 容器需要监听的端口号
            containerPort: int
            # 容器所在主机需要监听的端口号，默认与containerPort相同
            # (设置hostPort时，同一台宿主机将无法启动该容器的第二副本，由于端口占用问题)
            hostPort: int
            # 端口协议[TCP/UDP],默认为TCP
            protocol: string
        # 容器运行前需要设置的环境变量列表
        env:
            # 环境变量的名称
          - name: string
            # 环境变量的值
            value: string
        # 资源限制和资源请求的设置
        resource: 
            # 资源限制设置
            limits:
                # CPU限制(单位为：core)将用于docker run --cpu-shares参数
                cpu: string
                # 内存限制(单位为：MiB/GiB)将用于docker run --memory参数
                memory: string
            # 资源限制设置(请求)
            requests:
                # CPU请求(单位为：core)将用于容器启动的初始化可用数量
                cpu: string
                # 内存请求(单位为：MiB/GiB)将用于容器启动的初始化可用数量
                memory: string
        # 对Pod对象内各个容器进行安全检查的设置，当探测无响应几次后，将自动重启该容器
        # 包含[exec | httpGet | TcpSocket]三种方式，任选其一即可
        livenessProbe:
            exec:
                # 需要执行的脚本
                command: [string]
            httpGet:
                # 请求路径
                path: string
                # 请求端口
                port: number
                host: string
                scheme: string
                httpHeader:
                  - name: string
                    value: string
            tcpSocket:
                port: number
            # 完成容器启动后首次进行探测的时间(单位为：s)
            initialDelaySeconds: 0
            # 对容器健康检查探测等待超时时间(单位为：s)，默认值为1
            timeoutSeconds: 0
            # 对容器健康检查的探测时间周期(单位为：s)，默认值为10
            periodSeconds: 0
            successThreshold: 0
            failureThreshold: 0
        securityContext:
            privileged: boolean
    #
    # Pod对象的重启策略，可选值[Always | Never | OnFailure]
    #
    # Always: Pod对象一旦终止，则不关心容器是如何停止的，kubelet都将重器容器
    #
    # Never: Pod对象终止后，kubelet将退出码返回给Master，不再重启该容器
    #
    # OnFailure: 只有当Pod对象以非零退出码终止时，kubelet才会重启该容器
    # (容器正常结束的退出码为零)
    #
    restartPolicy: [Always | Never | OnFailure]
    # 表示将Pod对象调度到包含这些label的Node上(以key:value形式指定)
    nodeSelector: object
    # Pull镜像时使用的secret名称(以name:secretValue形式指定)
    imagePullSecrets:
      - name: string
    # 是否使用主机模式(默认值为:false)
    #
    # 如果设置为true，表示容器使用宿主机网络，不再使用Docker网桥
    # 该Pod对象将无法在同一台宿主机上启动第二个副本
    hostNetwork: boolean
    # 在该Pod对象上定义的共享储存卷列表
    volumes:
        # 共享储存卷名称，一个Pod对象中每个储存卷定义一个名称(命名应按照RFC 1035规范)
      - name: string
        # Pod对象同生命周期的一个临时目录，值为{}空对象
        emptyDir: {}
        # 挂载Pod对象所在宿主机的目录
        hostPath:
            # 将用于容器中mount的目录
            path: string
        # 挂载集群中预定义的secret对象到容器内部
        secret:
            secretName: string
            items:
              - key: string
                path: string
        # 挂载集群预定义的configMap对象到容器内部
        configMap:
            name: string
            items:
              - key: string
                path: string
```
#### 2.Pod的基本用法
Pod对象可以由1个或者多个容器组合而成。当两个或者多个容器应用为紧耦合关系，应该组合成一个整体对外提供服务，即将这两个容器打包为一个Pod对象。

##### 2.1 静态Pod对象
> 静态Pod对象是由Kubelet进行管理的仅存在于特定Node上的Pod对象。他们不可以通过API Server进行管理，无法与ReplicationController、Deployment或者DaemonSet进行关联，并且kubelet也无法对他们进行健康检查。静态Pod对象总是由kubelet进行创建，并且总是运行在kubelet所在的Node上运行。

创建静态Pod对象的两种方式：
+ 配置文件

    a.设置kubelet的启动参数**[--config]**: 指定kubelet需要监控的配置文件所在的目录，kubelet将会定期扫描该目录，并根据该目录下的\*.yaml或者\*.json进行创建.
    b.重启kubelet服务
    
+ HTTP方式

    设置kubelet启动参数**[--manifest-url]**: kubelet将会定期请求此URL下载Pod对象的定义文件，并以\*.yaml或\*.json文件格式解析，创建Pod对象。
    
##### 2.2 Pod对象容器共享卷

>在同一个Pod对象中的多个容器能够共享Pod对象级别的存储卷。Volume可以被定义为各种类型，容器各自进行挂载操作，将一个Volume挂载为容器内容存储卷。

`配置文件示例：`
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: volume-pod
spec:
    containers:
    - name: tomcat
        image: tomcat
        ports:
        - containerPort: 8080
        volumeMounts:
        - name: app-logs
            mountPath: /usr/local/tomcat/log
      - name: logreader
        image: busybox
        command: ["sh", "-c", "tail -f /logs/catalina*.log"]
        volumeMounts:
        - name: app-logs
            mountPath: /logs
    volumes: app-logs 
    - name: app-logs
        emptyDir: {}
```

##### 2.3 Pod对象的配置管理

> 为了提高应用部署的复用能力以及灵活性，可以将应用所需要的配置文件与程序进行分离。将应用打包为容器镜像后，可以通过环境变量配置、挂载外部文件的方式在创建容器时进行配置注入，但唯一的缺点**维护性**与**复杂性**将会在大规模容器集群中所体现。但在Kubernetes中可以通过**ConfigMap**进行管理。

1）ConfigMap的概念
+ 生成为容器内的环境变量
+ 设置容器的启动命令参数
+ 通过Volume的形式挂载到容器内部

ConfigMap以一个或者多个`[Key:Value]`的形式保存在Kubernetes系统中。可以通过`*.yaml`配置文件或者`kubelet create [-f configmap.yaml]`命令进行创建配置管理内容。

2）创建ConfigMap资源对象

`a) *.yaml实例`
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: cm-appvars
data:
    apploglevel: info
    appdatadir: /var/data    
```
`b) kubelet命令`
```shell
# 1.创建configmap.yaml配置文件
kubelet create -f cm-appvars.yaml
> configmap "cm-appvars" created

# 2.查看创建完成的配置文件
kubelet get configmap
#   NAME        DATA    AGE
#   cm-appvars   2       3s

# 3.查看指定配置的详细内容
kubelet get configmap cm-appvars -o yaml
```
