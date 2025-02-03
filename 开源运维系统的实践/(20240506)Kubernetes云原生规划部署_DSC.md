# Kubernetes云原生规划部署

 **Author:** [DSC]

 **Link:** [https://zhuanlan.zhihu.com/p/695798936]

 在了解k8s的时候，看到了很多大神、老师们的一些知识分享，学习到了很多内容，非常感谢。在此对个人规划部署使用做记录。 



---

## 云原生技术的意义  
云原生技术代表了软件开发和运维的现代化趋势，它通过微服务架构、容器化、DevOps实践和自动化来优化应用程序的构建、部署和运行。这种方法提高了应用的敏捷性、可靠性和可扩展性，同时降低了成本。随着技术的发展，云原生正在成为推动企业快速创新和全球化部署的关键动力，它不仅影响了技术层面，也深刻改变了商业模式和组织文化。

Kubernetes在于它针对现代云计算和应用部署领域的关键痛点提供了高效的解决方案。在传统的部署模式中，应用的扩展性、可用性和资源管理经常面临挑战，如手动管理大量服务器和服务、在高负载时维持性能稳定性，以及优化资源配置以减少浪费。Kubernetes作为一个先进的容器编排系统，通过自动化部署、扩展和管理容器化应用，有效解决了这些问题。



---

### 什么是云原生(Cloud Native)？  
云原生是一种构建和运行应用程序的方法，是一套技术体系和方法论, 如[https://www.baidu.com/s?wd=微服务架构&usm=4&ie=utf-8&rsv\_pq=d8d9d20d00010fdc&oq=云原生和k8s什么关系&rsv\_t=def0EZz5GoLfCbqOEpylYVqH7ghpUFYCnn8rFtQrIa2VaGLwcRKSMsbok7A&sa=re\_dqa\_zy&icon=1](https://www.baidu.com/s?wd=微服务架构&usm=4&ie=utf-8&rsv\_pq=d8d9d20d00010fdc&oq=云原生和k8s什么关系&rsv\_t=def0EZz5GoLfCbqOEpylYVqH7ghpUFYCnn8rFtQrIa2VaGLwcRKSMsbok7A&sa=re\_dqa\_zy&icon=1)、容器化、[https://www.baidu.com/s?wd=DevOps&usm=4&ie=utf-8&rsv\_pq=d8d9d20d00010fdc&oq=云原生和k8s什么关系&rsv\_t=def0EZz5GoLfCbqOEpylYVqH7ghpUFYCnn8rFtQrIa2VaGLwcRKSMsbok7A&sa=re\_dqa\_zy&icon=1](https://www.baidu.com/s?wd=DevOps&usm=4&ie=utf-8&rsv\_pq=d8d9d20d00010fdc&oq=云原生和k8s什么关系&rsv\_t=def0EZz5GoLfCbqOEpylYVqH7ghpUFYCnn8rFtQrIa2VaGLwcRKSMsbok7A&sa=re\_dqa\_zy&icon=1)、持续集成/持续交付（CI/CD），这些技术和方法的组合使应用程序能够更加高效、可靠和可扩展。。云原生(CloudNative)是一个组合词，Cloud+Native。Cloud表示应用程序位于云中，而不是传统的数据中心;Native表示应用程序从设计之初即考虑到云的环境，原生为云而设计，在云上以最佳姿势运行，充分利用和发挥云平台的

简单理解:从设计之初考虑，将原生程序以最好的方式运行在云上。

![]((20240506)Kubernetes云原生规划部署_DSC/v2-8f1b95e67af8ae83cd9fffa748206b47_1440w.jpg)  
云原生技术的主要优势在于其极高的灵活性和可扩展性，它通过微服务架构、容器化、DevOps文化和自动化工具，实现了快速部署、敏捷开发和高效运维。这种方法使应用更加健壮、可靠，并能够适应快速变化的市场需求和技术环境。云原生还优化了资源利用，降低了成本，同时提高了系统的可维护性和可观察性，为企业带来了更大的竞争优势和业务敏捷性。

### 云原生(Cloud Native)和Kubernetes有什么关系？  
云原生的理念则是围绕着提高应用程序的可移植性、弹性、效率和安全性展开的，它强调的是在云计算时代如何有效地利用基础设施和服务，同时保持软件的快速迭代和高生产率。

Kubernetes 作为一个平台，提供了诸如自动扩缩容、负载均衡、滚动更新等功能，以便于管理分布在不同服务器上的容器化的应用程序。

Kubernetes通过其自动负载均衡和服务发现功能，保证了应用在不同的负载条件下都能稳定运行。这意味着应用可以根据需求自动扩展，确保高可用性。其次，Kubernetes的自动化滚动更新和回滚机制减少了部署新版本时的停机时间，同时在出现问题时可快速恢复到稳定状态，增强了应用的持续部署和稳定性。此外，它通过智能的资源管理和调度，提高了服务器和服务的资源利用率，从而降低了成本。而Kubernetes的自我修复能力意味着它可以自动替换、重启或重新部署那些未正常工作的容器，大大减轻了维护负担。通过声明式配置，用户只需描述期望的目标状态，Kubernetes便能自动实现并维护这一状态，这简化了配置和自动化的复杂性。

### Kubernetes 快速入门  
Kubernetes 官方文档：[https://kubernetes.io/zh/](https://kubernetes.io/zh/)

### 简介  
Kubernetes 是一个可移植、可扩展的开源平台，用于管理容器化的工作负载和服务，可促进声明式配置和自动化。 Kubernetes 拥有一个庞大且快速增长的生态，其服务、支持和工具的使用范围相当广泛。

### Kubernetes 架构  
Kubernetes是属于主从设备模型（Master-Slave架构），即有Master节点负责核心的调度、管理和运维，Slave节点则执行用户的程序。在Kubernetes中，主节点一般被称为Master Node 或者 Head Node，而从节点则被称为Worker Node 或者 Node。

Master节点通常包括API Server、Scheduler、Controller Manager等组件（在官方文档中被称为Control Plane Components[2]，通常用Master组件代替），Node节点通常包括Kubelet、Kube-Proxy等组件（在官方文档中被称为Node组件）。

![]((20240506)Kubernetes云原生规划部署_DSC/v2-c9db85b3befaa7e06f8bb884451d5667_1440w.jpg)  
### Kubernetes 集群组件  
![]((20240506)Kubernetes云原生规划部署_DSC/v2-ac77615c7122cdeb23f54234423aea9c_1440w.jpg)  
![]((20240506)Kubernetes云原生规划部署_DSC/v2-41081f28b45f1a040bb8dbd3395bd6c5_1440w.jpg)  
### Kubernetes 部署规划设计  
### 环境  


| CentOS 7u9 | [https://mirrors.tuna.tsinghua.edu.cn/centos/7.9.2009/isos/x86\_64/CentOS-7-x86\_64-Minimal-2009.iso](https://mirrors.tuna.tsinghua.edu.cn/centos/7.9.2009/isos/x86\_64/CentOS-7-x86\_64-Minimal-2009.iso) |
| --- | --- |
| docker-ce | [http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo](http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo) |

### 硬件配置  


| CPU | 内存 | 硬盘 | 角色 | 主机名 |
| --- | --- | --- | --- | --- |
| 2C | 4G | 50G | Master | kmaster |
| 2C | 4G | 50G | Worker(node) | Knode1 |
| 2C | 4G | 50G | Worker(node) | Knode2 |

### 网络规划  
地址段规划



| 类别 | IP段 | 可以地址范围 | 可用IP数量 |
| --- | --- | --- | --- |
| K8S | 10.10.10.0/24 | 10.10.10.1-10.10.10.254 | 254 |
| pod | 10.244.0.0/16 | 10.244.0.1-10.244.255.254 | 65534 |
| service | 10.1.0.0/16 | 10.1.0.1-10.1.255.254 | 65534 |

![]((20240506)Kubernetes云原生规划部署_DSC/v2-3960cc18ed0b15dfcff9fa727539ca89_1440w.jpg)  


---

## 基于Docker-CE部署k8s  
### 主机配置  
本次使用3台主机完成kubernetes1.28.2 集群部署，

其中1台为master节点,名称为kmaster;其中2台为worker节点，名称分别为：knode1及knode2


```
master节点  
# hostnamectl set-hostname kmaster 
worker01节点
# hostnamectl set-hostname knode1
worker02节点
# hostnamectl set-hostname knode2
```
更换软件源

更换阿里云centos7软件源，因为是最小化安装系统，方便后续下载软件。


```
Wget - O /etc/yum.repos.d/CentOS-Base.repo 
https://mirrors.aliyun.com/repo/Centos-7.repo
yum makecache
```
### 主机IP地址配置  
k8s-worker1节点IP地址为：10.10.10.81/24


```
# vim /etc/sysconfig/network-scripts/ifcfg-ens192
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="none"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens192"
DEVICE="ens192"
ONBOOT="yes"
IPADDR="10.10.10.81"
PREFIX="24"
GATEWAY="10.10.10.121"
DNS1="8.8.4.4"
```
kmaster节点IP地址为：10.10.10.80/24


```
# vim /etc/sysconfig/network-scripts/ifcfg-ens192
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="none"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens192"
DEVICE="ens192"
ONBOOT="yes"
IPADDR="10.10.10.80"
PREFIX="24"
GATEWAY="10.10.10.121"
DNS1="8.8.4.4"
```
k8s-worker2节点IP地址为：10.10.10.82/24


```
# vim /etc/sysconfig/network-scripts/ifcfg-ens192
TYPE="Ethernet"
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="none"
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens192"
DEVICE="ens192"
ONBOOT="yes"
IPADDR="10.10.10.81"
PREFIX="24"
GATEWAY="10.10.10.121"
DNS1="8.8.4.4"
```
### 主机名与IP地址解析  
所有集群主机均需要进行配置。


```
# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.10.10.80 kmaster
10.10.10.81 knode1
10.10.10.82 konde2
```
防火墙配置

所有集群主机均需要进行配置。


```
关闭现有防火墙firewalld
systemctl disable firewalld
# systemctl stop firewalld
firewall-cmd --state
#not running
```
### SELINUX配置  
所有主机均需要操作。修改SELinux配置需要重启操作系统。


```
# sed -ri 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
```
**时间同步配置**

所有主机均需要操作。最小化安装系统需要安装ntpdate软件。


```
yum install ntpdate
ntpdate time1.aliyun.com
```
升级操作系统内核


```
导入elrepo gpg key
# rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
安装kernel-ml版本，ml为最新稳定版本，lt为长期维护版本
# yum install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm
设置grub2默认引导为0
# grub2-set-default 0
重新生成grub2引导文件
# grub2-mkconfig -o /boot/grub2/grub.cfg
更新后，需要重启，使用升级的内核生效。
# reboot
重启后，需要验证内核是否为更新对应的版本
# uname -r
```
### 关闭SWAP分区  

```
vim /etc/fstab
# /dev/mapper/centos-swap swap                   swap    defaults        0 0
```
在文件中上方所在行前进添加#

修改完成后需要重启操作系统，如不重启，可临时关闭，命令为swapoff -a



---

### 配置内核转发及网桥过滤  
所有主机均需要操作。


```
添加网桥过滤及内核转发配置文件
# cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
vm.swappiness = 0
EOF

加载br_netfilter模块
# modprobe br_netfilter

查看是否加载
# lsmod | grep br_netfilter
br_netfilter           22256  0
bridge                151336  1 br_netfilter
```
### 安装ipset及ipvsadm  
所有主机均需要操作。


```
安装ipset及ipvsadm
# yum -y install ipset ipvsadm
```
配置ipvsadm模块加载方式

添加需要加载的模块


```
# cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack
EOF
```
授权、运行、检查是否加载


```
# chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack
```
### 容器运行时 Docker-CE准备  
从Kubernetes 1.24开始，官方不再支持Docker作为其容器运行时环境。这是因为Kubernetes使用的是Container Runtime Interface (CRI)，而Docker不直接实现CRI。在此之前，Kubernetes通过一个名为Dockershim的适配层来与Docker交互，但从1.24版本开始，这个适配层被移除了。

然而，即使在Kubernetes不再直接支持Docker的情况下，你仍然可以在节点上安装Docker。这是因为：

1. 分离的运行时和工具链：Docker包括两个主要的部分，一是Docker Daemon，它是容器运行时环境；二是Docker CLI，它是一个命令行工具，用于构建和管理Docker镜像和容器。尽管Kubernetes不再使用Docker Daemon，但Docker CLI仍然是一个非常有用的工具，可以用来构建镜像和在本地运行和测试容器。
2. Containerd作为共同基础：Docker Daemon自身使用Containerd作为其容器运行时。因此，当你在Kubernetes节点上安装Docker时，实际上也安装了Containerd。Kubernetes可以直接与Containerd交互，而无需Docker Daemon的介入。


```
yum -y install yum-utils
# 设置yum源
yum-config-manager 
--add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# 安装docker-ce版本
yum install -y docker-ce
# 启动
systemctl start docker && systemctl enable docker
# 查看版本号
docker --version
# Docker镜像源设置
```
修改文件 /etc/docker/daemon.json，没有这个文件就创建

添加以下内容后，重启docker服务：


```
cat >/etc/docker/daemon.json<<EOF
{
   "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
EOF
# 加载
systemctl reload docker
# 查看
systemctl status docker containerd
```
## K8S集群部署实践  
### K8S集群软件YUM源准备  
更换阿里云kubernetes源


```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
setenforce 0
yum install -y kubelet kubeadm kubectl
systemctl enable kubelet && systemctl start kubelet
```
### 设置代理  
kubeadm部署与docker镜像拉取没有代理可能会失败。

### K8S集群软件安装  
所有节点均安装


```
yum -y install  kubeadm  kubelet kubectl
```
安装指定版本


```
# yum -y install  kubeadm-1.28.2-0  kubelet-1.28.2-0 kubectl-1.28.2-0
```
### 配置kubelet  

```
# vim /etc/sysconfig/kubelet
KUBELET_EXTRA_ARGS="--cgroup-driver=systemd"
```
设置kubelet为开机自启动即可，由于没有生成配置文件，集群初始化后自动启动


```
# systemctl enable kubelet
```


---

### kubernetes 1.28集群初始化  

```
kubeadm init \
  --apiserver-advertise-address=10.10.10.80 \
  --image-repository registry.aliyuncs.com/google_containers \
  --kubernetes-version v1.28.2\
  --service-cidr=10.1.0.0/16 \
  --pod-network-cidr=10.244.0.0/16 \
  --v=5
```


| 参数 | 描述 |
| --- | --- |
| apiserver-advertise-address | 向集群成员通知 apiserver 消息的 IP 地址。 这个地址必须能够被集群中其他成员访问。 |
| image-repository | 拉取控制平面镜像的容器仓库，使用的阿里云仓库地址 |
| kubernetes-version | 下载的k8s软件版本号 |
| service-cidr | 服务的虚拟 IP 地址另外指定 IP 地址段 |
| pod-network-cidr | 指明 pod 网络可以使用的 IP 地址段,如果设置了这个参数，控制平面将会为每一个节点自动分配 CIDRs。 |

使用kubeadm完成初始化会生成token，node节点可以通过token加入到集群当中。

![]((20240506)Kubernetes云原生规划部署_DSC/v2-9d8179bf24c1a5bf3a4bcf34e2ed86c7_1440w.jpg)  


---

### 工作节点加入集群  
将master内获得的token在工作节点内执行，所有节点需要。

![]((20240506)Kubernetes云原生规划部署_DSC/v2-e2c94cada321ef70b72beee6adc6ba62_1440w.png)  
### 网络插件flannel部署  
工作节点加入集群后无法正常运行，通过kubectl get nodes查看会发现节点是Notready状态，我们需要安装网络插件保障容器间的网络连接，CNI插件确保集群中的Pods可以相互通信，并与外部世界交换数据。它为Pods提供了独立的IP地址，支持跨节点通信，并实现了服务发现和负载均衡。此外，CNI还支持网络策略，使得可以控制Pods之间的访问权限，从而增强安全性。下面是两款常见的网络插件

calico访问链接：[https://projectcalico.docs.tigera.io/about/about-calico](https://projectcalico.docs.tigera.io/about/about-calico) 

![]((20240506)Kubernetes云原生规划部署_DSC/v2-a37fe3cb43e3dabe07a7792f52324c91_1440w.jpg)  
Flannel访问链接：[https://github.com/flannel-io/flannel](https://github.com/flannel-io/flannel)

![]((20240506)Kubernetes云原生规划部署_DSC/v2-585325eba711ddfe092dbbd6d38ca067_1440w.jpg)  
### 部署flannel插件  

```
#Kubectl apply -f 
https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```
确保pod网段与kubeadm初始化时相同


```
Cat kube-flannel.yaml
net-conf.json: |
    {
      "Network": "10.244.0.0/16",
      "Backend": {
        "Type": "vxlan"
      }
    }
```
查看K8S集群状态

![]((20240506)Kubernetes云原生规划部署_DSC/v2-8272ec2f45f7a669096a915585d8cc21_1440w.jpg)  
查看pod是否正常运行

![]((20240506)Kubernetes云原生规划部署_DSC/v2-9bfcaf3a558a13e0bd09df97aaf6dee9_1440w.jpg)  
查看flannel网络组件状态

![]((20240506)Kubernetes云原生规划部署_DSC/v2-49eb0c17e9aad2e8224aa0010c69a8d0_1440w.jpg)  
### K8S高可用验证  
K8S的一个重要特性是为云原生提供高可用，本次采用部署Pod节点，搭建Nginx应用，验证应用的高可用。

创建pod


```
kubectl create deployment web --image=nginx
```
查看pod列表


```
kubectl get pods -o wide
```
暴露资源为service


```
kubectl expose deployment web --port=80 --protocol=TCP --target-port=80 --name=nginx
```
查看service列表


```
kubectl get svc
```
![]((20240506)Kubernetes云原生规划部署_DSC/v2-2171c6eb8f548f7342c28710df956edf_1440w.jpg)  
![]((20240506)Kubernetes云原生规划部署_DSC/v2-188dded082cb5330e0afbb018aec5586_1440w.jpg)  
集群内任意节点IP地址访问nginx服务

![]((20240506)Kubernetes云原生规划部署_DSC/v2-247a9689514987a82ffd097b5cb97c42_1440w.jpg)  
![]((20240506)Kubernetes云原生规划部署_DSC/v2-2bd3357df3632bbdeff0be75a710beeb_1440w.jpg)  
master 节点负责控制和管理整个集群，它运行着一些关键的组件，如 kube-apiserver、kube-scheduler、kube-controller-manager 等。如果只有一个master节点，那么一旦它出现故障或者不可用，那么整个集群就会失去控制，无法进行任何操作。为了保证集群的高可用性，需要使用多个master节点来构建master集群。多master集群可以分为应用集群与存储集群。

![]((20240506)Kubernetes云原生规划部署_DSC/v2-a86032907f3665d738f73c37ef3e8cbb_1440w.jpg)  
新增master节点,生成token


```
#kubeadm token create --print-join-command
Kubeadm join 10.10.10.80:6443 --token oykmdy.cei9zdrjktizznir --discovery-token-ca-cert-hash sha256:e05bcf11413a9f9197ea8d6f42df5c28cfe7289734ca1aa914f2fd51a61b3dae
```
生成certificate-key


```
kubeadm init phase upload-certs --upload-certs
960f4a3ddebe9d0aa24a398bb901c8cc7810d519391d6fe8d3e1cf7b76e223a8
```
使用--control-plane --certificate-key拼接获得master 的join命令


```
kubeadm join 10.10.10.80:6443 --token s1m8gb.eisk1fvn6bubw5j4 --discovery-token-ca-cert-hash sha256:e05bcf11413a9f9197ea8d6f42df5c28cfe7289734ca1aa914f2fd51a61b3dae --control-plane --certificate-key 71b9f3a38bdf694f02e8de3737ed2d60d8ea297ac1546bf9822acdb8581d5843
```
查看集群信息,Kmaster2节点正常运作

![]((20240506)Kubernetes云原生规划部署_DSC/v2-8793feeeeceaf00dc84c5b58bd0fe2f2_1440w.jpg)  


---

**本人涉及资料来源于日常工作及互联网搜索，已做脱敏处理，如果仍存有涉及侵权内容，请告知。将做删除修改。如有错误，欢迎大家指正。**

