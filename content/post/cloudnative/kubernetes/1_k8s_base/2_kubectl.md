# 1-2  Kubectl命令行工具
### 1.kubectl用法
>$~: **kubectl  [command]   [TYPE]  [NAME]  [flags]**

+ **[command]** `子命令。用于操作Kubernetes集群资源对象。`

    可取值：[create | delete | describe | get | apply]
    
+ **[TYPE]** `资源对象的类型。区分大小写`
    
    备注：可以通过单数形式、复数形式、简写形式表示。
    ```shell
    # 例：不同写法的Type,但是效果一致
    kubectl get pod pod1
    kubectl get pods pod1
    kubectl get po pod1
    ```
+ **[NAME]** `资源对象名称。区分大小写`
    备注： 如果不指定名称，将返回属于**TYPE**的所有对象列表。
    ```shell
    # 例：返回所有对象列表
    kubectl get pods
    ```
    
+ **[flags]** `kubectl子命令的可选参数`

### 2.kubectl操作实例
+ 创建资源对象
    
    ```shell
    # 由配置文件(*.yaml)创建一次性对象
    # 创建一个对象
    kubectl create -f service.yaml
    # 创建对个对象
    kubectl create -f service.yaml -f pod.yaml
    ```
+ 查看资源对象

    ```shell
    # 查看所有Pod列表
    kubectl get pods
    # 查看指定对象
    kubectl get service,pod
    ```
+ 资源对象详情

    ```shell
    # 显示Node的详细信息
    kubectl describe nodes node1
    # 显示Pod的详细信息
    kubectl describe pods/service
    # 显示由node1管理的pod对象
    kubectl describe pods node1-service
    ```
+ 删除资源对象

    ```shell
    # 基于配置文件(*.yaml)定义中名称的Pod对象
    kubectl delete -f service.yaml
    # 删除包含指定label的所有Pod和Service对象
    kubectl delete pods,services -l name=label-obj
    # 删除所有Pod对象
    kubectl delete pods --all
    ```
+ 运行资源对象

    ```shell
    # 指定Pod对象的date命令，默认情况下在Pod对象的第一个容器中执行
    kubectl exec <pod-name> date
    # 指定Pod对象在某个特定容器中执行
    kubectl exec <pod-name> -c <container-name> date
    # 通过bash获取Pod对象中特定容器的TTY(可以理解为登录容器)
    kubectl exec -ti <pod-name> -c <container-name> 
    ```
+ 查看容器日志

    ```shell
    # 查看容器输出到stdout日志
    kubectl logs <pod-name>
    # 跟踪查看容器日志(与tail -f命令具有相同效果)
    kubectl logs -f <pod-name> -c <container-name>
    ```
        