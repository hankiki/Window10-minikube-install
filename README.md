# Window10-minikube-install
引用我的CSDN文档-Windows 10 Kubernetes(minikube)搭建(https://blog.csdn.net/hanjupiter/article/details/100183520)

Kubernetes是什么？
Kubernetes (K8s) 是开源的集群管理工具，用来管理集群中容器对象，自动实现负载均衡。

前言
Windows 10 Docker的环境搭建
https://blog.csdn.net/hanjupiter/article/details/100173356

在Linux系统中，使用kubeadmin来管理多个集群节点。 例如：一个master主机，两个node节点，组成1个对外的主机
在Windows系统中，使用minikube 来进行**单节点模式**管理集群

在微服务中，将各个接口剥离开后，将每一个业务接口按照业务量的多少 来创建多个容器，在Kubernetes 中自动实现负载均衡 在相同容器间的访问资源。
    如:传统形式下，创建2台主机，将所有的业务（登录，用户数据查询，下单等）通过一个war 部署到tomcat中，由于主机的接口是唯一的，当登录和用户数据查询接口被大量访问时，会造成大量资源被占用，此时想增加tomcat服务，需要修改代理服务设定，才能响应。
    而在Kubernetes 中，可以将各接口按使用量来创建对应个数的容器，负载均衡的方式进行访问，如登录 8个容器，用户数据查询 10个容器，下单 5 的容器，这时当大量的用户数据查询发生时，也不会将其他的业务影响到，并且如果某个业务的容器资源占用率高时，可以随时添加新的容器，与原来的容器组进行合并，扩大负载均衡容器的数量，保证业务稳定运行，对外的接口不需要进行变更。

在一个集群中，通过Kubernetes ，来创建容器集群(POD),service。外部网络调用到service，service调用POD中的docker容器。

# 1.官方链接
   1.**kubernetes官网中minikube安装文档** [网址跳转](https://kubernetes.io/docs/setup/learning-environment/minikube/)
   2.**minikube官方文档** [网址跳转](https://minikube.sigs.k8s.io/docs/start/)

# 2.下载
**minikube官网** [下载地址跳转](https://kubernetes.io/docs/tasks/tools/install-minikube/) 
下载方式有2种:
1. 使用windows 安装器
```powershell
minikube-installer.exe
```
 2. 使用直连的方式，进入到GIT项目中，进行下载
```powershell
minikube-windows-amd64
```

### 下载方式1
在GIT中找到minikube-installer.exe的安装包，进行下载
**当前下载的是v1.6.2版本**
下载地址:[v1.6.2-minikube-installer](https://github.com/kubernetes/minikube/releases/download/v1.6.2/minikube-installer.exe)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126213913368.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
下载后的文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126214139813.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126214126277.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126214156732.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126214225320.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126214241547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126214301819.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126214311572.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
### 下载方式2
不进行介绍

# 3创建minikube集群
文档链接 [启动minikube](https://minikube.sigs.k8s.io/docs/start/windows/)
准备：
创建hyper-v的虚拟网卡，指向到本机的网卡上
1.开始中搜索hyper-v，并打开Hyper-V管理器
2.打开虚拟交换机管理器
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200203093448643.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
3.创建虚拟交换机，选择外部
minikube服务器与主机为同一个网段，并且设定静态IP后，重启电脑后，依然可以正确访问，否则重启电脑后，IP地址发生变更，无法访问。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200203093612624.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)

4 虚拟交换机设定名称，当前设定的是minikube-net![在这里插入图片描述](https://img-blog.csdnimg.cn/20200203093905187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
**注：
如果是Realtek厂商的网卡，直接选用外部网络，无任何问题。
如果是Intel厂商的网卡，直接选用外部网络后，会导致本机网卡配置移动到虚拟网卡中，本地网卡无法上网。
解决方法：**
1.在虚拟网卡中，修改虚拟网卡的IP地址。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020020911250296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
2.修改虚拟网卡的MAC地址
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200209112943778.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
3.恢复本地网卡的IPV4及IPV6协议，设定DNS
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200209113500757.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
#### 3.1 启动 Minikube 并创建一个集群（kubernetes版本号1.16）
※当前最新版本为1.17，由于镜像效验问题，无法正确安装，则使用1.16版本
```powershell
minikube start --vm-driver=hyperv  --memory=4096 --disk-size='30000mb' --image-repository="registry.cn-hangzhou.aliyuncs.com/google_containers" --hyperv-virtual-switch="minikube-net" --kubernetes-version v1.16.0

解释:
minikube start 创建默认集群/启动已经关闭的默认集群
--vm-driver=hyperv 指定虚拟机类型，win10是hyperv
--memory=4096  指定服务器的内存大小至4GB，参考下记的问题4，默认为2GB
--disk-size='30000mb' 多个镜像及容器启动占用大量磁盘，初始值增大服务器的磁盘容量
--image-repository="registry.cn-hangzhou.aliyuncs.com/google_containers" 镜像下载地址重定向，参考下记的问题1和2
--hyperv-virtual-switch="minikube-net" 使用指定的虚拟交换机
--kubernetes-version v1.16.0 指定的kubernetes版本

```
安装时间非常漫长，当提示kubeadm，kubelet 下载失败后，使用问题3的解决方案即可。 

#### 3.2 虚拟机驱动名称一览
 [一览列表](https://minikube.sigs.k8s.io/docs/reference/drivers/)

#### 3.3  设定hyperv默认驱动器
```powershell
minikube config set vm-driver hyperv
```
#### 3.4 集群内存设定
minikube 默认内存设定为2G，扩大内存命令
```powershell
minikube config set memory 4096
```
#### 3.5 问题一览
使用默认启动命令时，会遇到下面的问题
```powershell
minikube start --vm-driver=hyperv
```
**问题1**
minikube系统 下载速度慢
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020012622105877.png)
或者无法下载minikube.iso
![](https://img-blog.csdnimg.cn/20200206075756616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
解决方案
登录minikube的git库，下载指定版本的镜像文件，例如:当前提示为minikube-v1.7.0.iso
保存到用户下的缓存路径中，例如:C:\Users\Administrator\.minikube\cache\iso
```bash
https://github.com/kubernetes/minikube/releases
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200206080157202.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20200206080634490.png)
**问题2**
无效的内存地址或空指针异常
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200126223528665.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
**解决方案**：
镜像及容器替代路径:
```powershell
--image-repository="registry.cn-hangzhou.aliyuncs.com/google_containers" 
```
**问题3**
kubeadm 和 kubele联网下载失败
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200127144925279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
**问题3的解决方案**：
使用第三方工具进行下载,存储至user下的{user}\.minikube\cache\v1.16.0文件夹中
例如：
```powershell
C:\Users\Administrator\.minikube\cache\v1.16.0
```
```powershell
kubeadm http://storage.googleapis.com/kubernetes-release/release/v1.16.0/bin/linux/amd64/kubeadm

kubelet http://storage.googleapis.com/kubernetes-release/release/v1.16.0/bin/linux/amd64/kubelet
```
存储路径：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020012715204895.png)
再次启动minikube，成功安装

```powershell
minikube start --vm-driver=hyperv --memory=4096 --disk-size='30000mb' --image-repository="registry.cn-hangzhou.aliyuncs.com/google_containers" --hyperv-virtual-switch="minikube-net" --kubernetes-version v1.16.0
```
![加粗样式](https://img-blog.csdnimg.cn/20200127152123193.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
##### 问题4
apiserver无法响应
1.未重启WIN10系统的场合，minikube服务器的IP地址没有发生变更
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200202143154630.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200202143111973.png)
解决方法：
增大 minikube 服务器的内存
```bash
--memory=8192
```
2.重启WIN10系统的场合，hyper-v的内部虚拟网卡及minikube的服务器的IP地址发生了变更。

解决方法：
需要minikube绑定静态IP地址，设定指定Vlan的虚拟网卡
```bash
--hyperv-virtual-switch="minikube-net"
```
IP地址恢复
```bash
minikube ssh "sudo ifconfig eth0:0 IP地址"
例如：minikube ssh "sudo ifconfig eth0:0 192.168.1.105"
```
##### 问题5
[1.7.0](https://github.com/kubernetes/minikube/releases/download/v1.7.0/minikube-installer.exe)版本的minikube工具，无法正确创建minikube的服务器主机
提示错误：
```powershell
'minikube delete': create: provisioning: Temporary Error: mkdir -p C:\Users\Administrator\.minikube: exec: "mkdir": executable file not found in %PATH%
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200206112843134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
**解决方案**
使用1.6.2版本的minikube工具
下载地址:[v1.6.2-minikube-installer](https://github.com/kubernetes/minikube/releases/download/v1.6.2/minikube-installer.exe)
#### 3.6 启动minikube控制台
生成kubernetes控制台网址
```powershell
minikube dashboard
```
###### ※1.重启电脑/退出当前命令行，网页无法进行访问，需要重新生成url地址
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200127152524420.png)

使用网址登录到kubernetes网页控制台
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020012715262021.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
在Nodes选项卡中，minikube的集群节点已经创建
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200127152857370.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
#### 3.7 查询minikube集群中已经下载的容器镜像
```powershell
登录到集群
minikube ssh
查看集群中已经下载的镜像 
docker images
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020012713382368.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
minikube安装基础环境包
路径：/var/lib/minikube/images/
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020012713565355.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)

#### 3.8 删除minikube集群节点
```powershell
minikube delete
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200127131801464.png)
#### 3.9 查看minikube集群节点状态
```powershell
minikube status
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200129090740824.png)
#### 3.10 查看kubernetes集群节点(node)状态
```powershell
kubectl get node
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200129120217705.png)
#### 3.10 查看kubernetes集群节点组件
```powershell
kubectl get pod -n kube-system
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200129120709799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hhbmp1cGl0ZXI=,size_16,color_FFFFFF,t_70)
#### 3.11 停止minikube集群节点
```powershell
minikube stop
```
![minikube stop](https://img-blog.csdnimg.cn/20200128111117338.png)
#### 3.12 创建新的minikube集群节点
```powershell
minikube start -p 集群节点名称
```
#### 3.13 重启已经存在的minikube集群节点
```powershell
默认集群
minikube start --vm-driver=hyperv --image-repository="registry.cn-hangzhou.aliyuncs.com/google_containers" --kubernetes-version v1.16.0
指定名称的集群
minikube start -p 集群名称 --vm-driver=hyperv --image-repository="registry.cn-hangzhou.aliyuncs.com/google_containers" --kubernetes-version v1.16.0
```

#### 3.14 配置minikube的docker镜像加速
由于minikube中下载registry-1.docker.io/v2/library/站点中的docker镜像非常缓慢，导致创建容器失败，需要配置docker的加速配置
※注：不要配置本地仓库 insecure-registries，因为/lib/systemd/system/docker.service文件中已经有相关的配置，如果重复设定会导致docker重启失败。
```powershell
1.登录到minikube中
minikube ssh
2.切换到/etc/docker/路径下
$ cd /etc/docker/
3.切换到root权限
$ sudo su root
4.创建并编写daemon.json文件
$ cat > daemon.json <<EOF
5.提示 > 时，粘贴下面的配置 
{
  "registry-mirrors": [
    "https://mirror.ccs.tencentyun.com",
    "https://hub-mirror.c.163.com",
    "https://registry.docker-cn.com",
    "https://dockerhub.azk8s.cn",
    "https://docker.mirrors.ustc.edu.cn"
  ]
}
6.输入EOF ，退出并保存
>EOF
7.重启docker
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```
