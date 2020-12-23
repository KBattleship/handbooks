# 安装初始化K8s集群
## 1.安装K8s
### 1.1CentOS安装
+ 预先准备工作

    ```shell
    # 修改设置主机名称
    hostnamectl set-hostname master
    # 绑定主机各节点hosts
    192.168.0.1 master
    192.168.0.2 node1
    192.168.0.3 node2
    # 验证每节点的Mac地址与UUID是否唯一
    # mac地址注意查看网卡
    cat /sys/class/net/eth1/address
    cat /sys/class/dmi/id/product_uuid
    # 关闭缓存交换swap
    swapoff -a  # 临时关闭
    sed -i.bak '/swap/s/^/#/' /etc/fstab    #永久关闭
    ```
    
+ 安装Kubernetes
    ```shell
    # 设置K8s安装源，由于防火墙问题使用阿里云源
    cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
    # 更新源缓存
    yum clean all
    yum -y makecache
    
    # 查看k8s版本
    yum list kubelet --showduplicates | sort -r 
    
    # 默认安装最新版本
    yum install -y kubelet kubeadm kubectl
    # 选择指定版本进行安装
    yum install -y kubelet-<version> kubeadm-<version> kubectl-<version>
    ```

### 1.2MacOS安装
```shell
Docker-Desktop版内置(需要访问科学上网)
```
## 2.准备Kubernetes依赖镜像
```shell
k8s.gcr.io/kube-apiserver:v1.17.1
k8s.gcr.io/kube-controller-manager:v1.17.1
k8s.gcr.io/kube-proxy:v1.17.1
k8s.gcr.io/kube-scheduler:v1.17.1
k8s.gcr.io/coredns:v1.17.1
k8s.gcr.io/etcd:v1.17.1
```
由于国外站点问题，需要科学上网，或者通过其他镜像仓库拉去，然后通过`docker tag`打标签的形式保存在docker仓库
```shell
# 通过科学上网拉去官网仓库镜像
docker pull k8s.gcr.io/kube-apiserver:v1.17.1
...
k8s.gcr.io/etcd:v1.17.1
## 然后通过本地惊醒仓库打包导出tar的形式
docker save k8s.gcr.io/kube-apiserver:v1.17.1 > k8s.tar.gz
## 导入目标服务器的本地镜像仓库
docker load < k8s.tar.gz
```

## 3.使用kubeadm初始化集群主节点
+ 初始化主节点
    ```shell
    kubeadm init –apiserver-advertise-address=192.168.66.176 --kubernetes-version=v1.17.1 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --ignore-preflight-errors=Swap
    
    # 增加Kubernetes本地全局变量配置
    ### 非root用户
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    ### root用户
    echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> ~/.bash_profile
    source .bash_profile
    ```
+ 初始化主节点网络

    ```shell
    # 使用flannel配置网络
    kubectl apply -f kube-flannel.yml
    ```
+ 关于节点污点问题
    > taint:污点的意思.如果某节点设置为污点，那么pod将不允许在此节点上运行。
    
    ```shell
    # 查看污点信息
    kubectl describe node master|grep -i taints
    # 删除默认污点
    kubectl taint nodes master node-role.kubernetes.io/master-
    # 设置污点
    kubectl taint node master key1=value1:NoSchedule
    # 删除污点
    kubectl taint nodes master  key1-    
    ### 关于污点语法
    kubectl taint node [node] key=value[effect]   
     其中[effect] 可取值: [ NoSchedule | PreferNoSchedule | NoExecute ]
      NoSchedule: 一定不能被调度
      PreferNoSchedule: 尽量不要调度
      NoExecute: 不仅不会调度, 还会驱逐Node上已有的Pod
    ```
+ Node节点加入集群
    
    + 查看令牌
        ```shell
        kubeadm token list
        ```
        
        `如果令牌过期可以重新生成令牌`
    + 初始化令牌
        ```shell
        kubeadm token create
        ```
    + 生成新的加密串
        ```shell
        openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
        ```
    + node加入master
        ```shell
        kubeadm join 192.168.66.175:6443 --token uirohl.1auw4f6ebu1c1etc \
    --discovery-token-ca-cert-hash sha256:f0d231c5a175c4f84d94cf0d7df2efc96e4ac396482ed9e04880a9d2c9b6a84e 
        ```
+ 集群移除Node节点
    ```shell 
    ### 已验证
    
    # 设置节点不可调度
    kubectl cordon ${Node}
    # 恢复节点调度
    kubectl uncordon ${Node}
    
    # 驱逐节点上运行的业务容器
    kubectl drain --ignore-daemonsets ${Node}
    
    # 移除节点
    kubectl delete node ${Node}
    
    ### master剔除node（待验证）
    etcdctl --cacert=/etc/etcd/pki/ca.pem --cert=/etc/etcd/pki/server.pem --key=/etc/etcd/pki/server-key.pem --endpoints=https://210.74.13.8:2379 del /registry --prefix
    ```
    