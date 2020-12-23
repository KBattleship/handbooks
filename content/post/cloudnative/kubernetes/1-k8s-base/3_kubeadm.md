# 3.K8S-kubeadm命令使用

## 一、kubeadm概述
```shell
$~:kubeadm --help

# kubeadm [command]
    |———— alpha [command]   
    |———— completion
    |———— config
            |———— images
                    |———— list 列出所有依赖镜像
            |———— pull 
    |———— help 查看命令详细描述
    |———— init 初始化Kubernetes集群Master
    |———— join 在Kubernetes集群中增加Node
    |———— reset 重置Kubernetes集群
    |———— token
    |———— upgrade
    |———— version
```

