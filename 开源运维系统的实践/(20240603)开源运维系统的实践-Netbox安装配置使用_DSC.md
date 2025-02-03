# 开源运维系统的实践-Netbox安装配置使用

 **Author:** [DSC]

 **Link:** [https://zhuanlan.zhihu.com/p/656805361]



---

最新netbox4.0可以参考[https://www.zhihu.com/people/038b98206f6f5df24146afc8e1ecfc15](https://www.zhihu.com/people/038b98206f6f5df24146afc8e1ecfc15) 的系列教程，同时也包含docker的一键部署及汉化。

[分类 - NetBox](https://songxwn.com/categories/NetBox/)### 文档意义  
对于企业IT而言，日趋复杂的IT业务使得运维团队最基本的IT资产梳理、排查工作都变得异常艰难，业务网络场景和真实网络之间如何做到如实映射？网络上有什么？繁杂的网络设备涵盖交换机，路由器，无线AP以及防火墙还有物联网设备，IPT/呼叫中心，各式服务器与虚拟机集群，IPv6带来的海量地址也使得管理困难。日益增长的网络复杂性可能会让运维团队崩溃。

用传统的模式? Excle表格登记 or 机柜正面拍图再标记？

市面上的大多数的CMDB系统基本上是传统的资产登记/盘点模式，和运维工程师的日常工作存在脱节，一旦网络环境变化，系统无法自动更新或者是需要定期滞后更新。

最理想的方式是投入一定规模的人力和资金开发一套定制化，实现CMDB和自动化运维结合的管理系统。不过投入产出比往往令CIO望而却步、知难而退。

Netbox定义自己是“一个 IP 地址管理（IP address management，IPAM）和数据中心基础设施管理（data center infrastructure management，DCIM）工具”。

从使用体验上，他试图通过数据模型复刻物理网络，在Netbox内准确的反映出现实世界的网络， Serve as a "Source of Truth"，把网络的理想状态和真实运行状态做一个匹配，听上去有点类似网络界的BIM（建筑信息模型Building Information Modeling），帮你定义和标识包括数据中心具体位置，不同的厂商、设备、接口、线缆等基础信息。并针对接口进行地址管理。同时支持Yaml等配置文件与脚本实现自动化收集网络基础信息，提供自定义与扩展功能。

本文档通过将开源运维系统Netbox和ALE网络设备进行结合，搭建一个支持自动运维的IPAM/DCIM的运维系统雏形，帮运维团队定义标识好所管理的数据中心/机房/机架/设备类型/接口信息/对接的业务（广域网链路，虚拟化系统等），而且还可以通过Python自动化编程运维DevOps ，结合NAPALM（Network Automation and Programmability Abstraction Layer with Multivendor support）对多品牌多厂商的设备/系统进行运行验证与自动化收集信息，对运维工程师而言，即使身处数据中心之外，仍对数据中心内部网络了如指掌。

### 文档说明  
此文档是开源运维系统的实系列之---Netbox安装配置及使用。

### 内容描述  
此文档是关于如何将开源软件Netbox安装部署到Ubuntu服务器内，并在Netbox内添加设备进行IPAM管理。其中包括：

1. 安装PostgreSQL
2. 安装Redis
3. 安装Python
4. 安装Netbox
5. 安装Nginx
6. 添加并准确标识网络（以ALE网络设备）

### 测试环境介绍  


| 所需软件 | 用途 | 下载地址 |
| --- | --- | --- |
| Ubuntu | 20.04 | [https://mirrors.aliyun.com/oldubuntu-releases/releases/20.04.0](https://mirrors.aliyun.com/oldubuntu-releases/releases/20.04.0) |
| Netbox | V3.5.8 | [https://github.com/netbox-community/netbox](https://github.com/netbox-community/netbox)或Wget/git命令下载 |
| Python | 3.8.10 | [https://www.python.org/downloads/](https://www.python.org/downloads/)或apt下载 |
| Nginx | 1.18.0 | [http://nginx.org/en/download.html](http://nginx.org/en/download.html)或apt下载 |
| Redis | 5.0.7 | [https://redis.io/download/](https://redis.io/download/)或apt下载 |
| PostgreSQL | 12.16 | [https://www.postgresql.org/](https://www.postgresql.org/)或apt下载 |

测试硬件环境



| CPU/Memory | harddisk |
| --- | --- |
| 2 CPU cores/4GB | 50GB |

推荐4CPU cores/8GB，100GB硬盘



---

备注1：Netbox下载地址

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-2e3dd8cfd2dac283d2be9e2caeb33535_1440w.jpg)  
[https://github.com/netbox-community/netbox](https://github.com/netbox-community/netbox)

[https://gitee.com/mirrors/NetBox](https://gitee.com/mirrors/NetBox)

备注2：活跃度

Netbox目前最新版本 [https://gitee.com/mirrors/NetBox/commit/e61795d5c66c8c9121177a1ece0df71835ebe723](https://gitee.com/mirrors/NetBox/commit/e61795d5c66c8c9121177a1ece0df71835ebe723)，活跃度较高，最新更新日期为2023年8月24日

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-bfadb13c02cbe975b49b02e7fa21672a_1440w.jpg)  
Zabbix 在Github上有3.2K星

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-ca65f9bc880c929947fa4b4abc5da85c_1440w.jpg)  
Netbox在Github上有13.1K星.

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-993b4c82c18dedead9c9e616e4f8b93a_1440w.jpg)  
## 安装部署过程  
### 安装PostgreSQL  
备注：PostgreSQL是一个功能非常强大的、源代码开放的客户/服务器关系型数据库管理系统（RDBMS）

安装PostgreSQL数据库：


```
sudo apt install -y postgresql  
```
查看PostgreSQL版本信息


```
psql -V  #大写V  
```
![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-62c63cd842fe96b6e2096dced4bb9bd6_1440w.png)  
启动数据库并设置为开机自启：


```
sudo systemctl start postgresql  
sudo systemctl enable postgresql 
```
创建数据库与配置用户


```
# 进入数据库  
sudo -u postgres psql  
# 建库授权  
CREATE DATABASE netbox;  
CREATE USER netbox WITH PASSWORD '123456';  #红色字体自定义用户名与密码
GRANT ALL PRIVILEGES ON DATABASE netbox TO netbox;  
# 退出  
\q  
```
验证数据库状态


```
psql --username netbox --password --host localhost netbox  
```
![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-7da09434d728769f99deb5aad577a2d7_1440w.png)  
进入PostgreSQL后通过 \conninfo查看会话的连接信息

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-6835b38c90ec1e2e85816e7fc3f5b6f6_1440w.png)  
可以看到数据库已经可以连接，信息为：host "localhost" (address "127.0.0.1") at port "5432"

  


### 安装Redis  
备注：Redis是一个高性能的key-value数据库。redis的出现，很大程度上补偿了memcached这类key/value存储的不足，在部分场合可以对关系数据库起到很好的补充作用。

下载Redis


```
sudo apt install -y redis-server  
```
查看Redis版本信息 


```
redis-server -v  
```
![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-5ac2bb254675040ad78e16a1ddec0708_1440w.png)  
验证服务状态

使用 redis-cli ping 来验证，返回 PONG 表示正常。

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-7a181ad667bfb6710cc0a6f7cb8977fc_1440w.png)  
  


### 安装Python及相关依赖  
NetBox v3.0 开始最低支持 Python3.7，从 NetBox v3.2 开始将最低支持 Python3.8。

最新的 Ubuntu20.04 默认的 Python 版本为 3.8.10，但是仍缺少一些相关的依赖包。

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-cad9ddf0f4c6d447f4f0beedc10551e1_1440w.png)  
下载Python及依赖包


```
sudo apt install -y python3 python3-pip python3-venv python3-dev build-essential libxml2-dev libxslt1-dev libffi-dev libpq-dev libssl-dev zlib1g-dev  
```


---

## 安装配置Netbox  
### 下载Netbox  
先创建一个新文件夹，再通过git 拉去github内的项目


```
sudo mkdir -p /opt/netbox/  
cd /opt/netbox/  
sudo git clone -b master --depth 1 https://github.com/netbox-community/netbox.git .  
```
  


### 为NetBox创建系统用户  
备注：创建系统用户的目的是为了将 WSGI 和 HTTP 服务配置为在这个帐户下运行。


```
sudo adduser --system --group netbox  
```
将 /opt/netbox/netbox/media/ 目录设置成 netbox 用户权限，这个文件夹下是前端页面


```
sudo chown --recursive netbox /opt/netbox/netbox/media/  
```
  


### 修改NetBox配置文件  
复制一份默认的配置文件：


```
cd /opt/netbox/netbox/netbox/  
sudo cp configuration.example.py configuration.py  
```
生成一个密钥，用来维护 HTTP 用户会话状态，配置文件里面要用到，请保存 


```
python3 ../generate_secret_key.py   
```
进入Netbox配置文件修改参数


```
sudo vim  configuration.py  
```
初始化做最小配置只用修改几个参数即可，以下是列举

第 11 行：允许谁可以访问前端页面


```
ALLOWED_HOSTS = ['*']  
```
第 15~22 行：数据库，修改用户名和密码为之前设置的信息


```
DATABASE = {   
 'NAME': 'netbox',  
 'USER': 'netbox', # 修改用户名 
 'PASSWORD': '123456',  # 修改密码 
 'HOST': 'localhost',  
 'PORT': '',  
 'CONN_MAX_AGE': 300,  
}  
```
第 60 行：SECRET\_KEY


```
SECRET_KEY = '填写上面步骤所生成的密钥' 
```
第 203 行：修改时区，默认是 UTC，需要改为中国标准时间 


```
TIME_ZONE = 'Asia/Shanghai' 
```
  


### 安装其他可选的组件  
NetBox 还支持一些可选的软件包。如果需要，第一次安装的时候这些包必须在 NetBox 根目录的 local\_requirements.txt 中写上，这样 NetBox 会自动进行安装；或者也可以后期按需手动安装。

官方提供了napalm 组件用于从网络设备获取实时数据


```
sudo sh -c "echo 'napalm' >> /opt/netbox/local_requirements.txt" 
```
  


### 运行升级脚本  
运行自带的 upgrade.sh 来完成以下操作：

* 创建 Python 虚拟环境
* 安装所有必需的Python 包（根目录下 requirements.txt 和 local\_requirements.txt 包含的软件）
* 运行 Django 的数据库迁移
* 在本地生成离线文档
* 在磁盘上聚合静态资源文件

使用pip安装，如果是升级Netbox，完成后需要重启服务，新安装可以直接进行下一步。


```
sudo /opt/netbox/upgrade.sh  
```
  


### 创建django管理员用户  
NetBox 没有默认用户，所以需要创建一个超级用户（管理帐户）才能登录 NetBox。

进入虚拟环境，之后命令行前面都会有个 (venv) 的提示符，表示当前在虚拟环境中


```
source /opt/netbox/venv/bin/activate  
```
![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-3b3a8e2817d2c126d6f3b05f19f45310_1440w.png)  
创建超级用户，随后填写用户名密码等，邮箱可不填。


```
cd /opt/netbox/netbox  
python3 manage.py createsuperuser  
```
  


### 设置自动清理任务  
NetBox 包含了 housekeeping 管理命令用来处理一些定期清理任务，例如清除旧会话和过期的更改记录。使用以下命令来为脚本创建一个 cron 任务，注意脚本的路径，如果 NetBox 安装在其他路径，需要使用正确的绝对路径。


```
cd /opt/netbox/netbox  
python3 manage.py createsuperuser  
```


---

### 测试运行  
NetBox 已经安装的差不多了，可以使用 django 的命令来测试运行一下，没有问题后再使用 WSGI 来启动服务。


```
python3 manage.py runserver 0.0.0.0:8000 --insecure  
```
通过服务器的 8000 端口测试：http://localhost:8000

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-0b7b7aa5cab87a34c04ff030b32a0fb7_1440w.jpg)  
备注：实际部署时，需要结合 WSGI 来运行服务，必要时还需要配置 nginx 来使用。

  


### 使用 Gunicorn 运行 Django 服务  
NetBox 作为 HTTP 服务器后面的 WSGI 应用程序运行。 NetBox 默认使用的是 gunicorn，但是它不能处理静态文件，还要结合 nginx 才能正常使用，而 uwsgi 支持静态文件的映射。这里先使用官方提供的 gunicorn 来启动服务。 


```
sudo cp /opt/netbox/contrib/gunicorn.py /opt/netbox/gunicorn.py  
sudo cp -v /opt/netbox/contrib/*.service /etc/systemd/system/  
sudo systemctl daemon-reload  
```
启动服务并设置为开机自启动。


```
sudo systemctl start netbox netbox-rq  
sudo systemctl enable netbox netbox-rq  
```
查看服务状态


```
systemctl status netbox.service  
```
**---**### 安装nginx 处理静态文件  
如果本地有多个 web 服务，有 Python、php、java 编写的，都想监听 80 端口，这个时候就必须有一个负责转发（或负载均衡）的服务，例如采用Nginx 。

如果本机确定只跑这一个服务，可以简单处理，例如采用默认的uwsgi。但是 uwsgi 和 gevent 对于静态资源处理的并不是很好，一是性能问题，二是各种 HTTP 请求缓存头，处理的也没有 Nginx 完善。然后还有一些安全问题，Nginx 作为专业服务器，暴露在公网相对比较安全，uwsgi 和 gevent 的话，漏洞恐怕只比 Nginx 多而不是少。

安装 nginx


```
sudo apt install -y nginx  
```
修改Nginx配置文件，可参考下图配置


```
vim /etc/nginx/conf.d/netbox.conf  
```
![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-78ada5e900bec2bd2a85312b54882f5d_1440w.jpg)  


| 1 | 监听80端口 |
| --- | --- |
| 2 | 静态文件映射 |
| 3 | 80 端口的访问请求转发到 gunicorn 的服务上 |

重启nginx服务


```
sudo systemctl restart nginx  
```


---

测试通过80端口访问，[http://localhost/](http://localhost/)

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-f1f507a778f193cd7faf3f8b14776d0a_1440w.jpg)  
## Netbox Usage  
1. 创建region（地区）
2. 创建site（站点，region>site）
3. 创建location（比如在哪栋楼/哪层楼/哪个房间等）
4. Manufacturers （设备厂家名字，比如ALE/思科/华为/华三等）
5. Device Types （每个厂家等设备都有型号）
6. Platforms （设备软件平台，比如思科的ios/nxos/ios-xe等）
7. Device Roles （定义设备角色，比如access/core/aggregate/border等）
8. Devices (添加设备)
9. interfaces（在deviceadd component中添加接口）
10. 添加IP 关联设备接口
11. 演示效果

### 在组织机构地区内新建地区  
![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-85bf1352ce130a0f305602f066545a36_1440w.jpg)  


---

**创建site（站点，region>site）**  
组织机构数据中心(Sites)

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-d418f6f5d295111b58f2f9c8ad4bc32b_1440w.jpg)  
**创建location**  
组织机构具体地点(Location)

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-ed315d8e115d84a7e8154bd6a997caf6_1440w.jpg)  


---

**设置Manufacturers**  
设备制造商(Manufacturers)

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-b557c4ecabe71b16b484b635a5c19043_1440w.jpg)  
**Device Types**   
设备设备类型

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-882bce26a8b10cce1af04a65e6310a4b_1440w.jpg)  


---

再对设备类型添加细节如：厂商,类型名称，软件平台，设备前后图片等。

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-b8b73fd174f7905a2f2472c2703b33e7_1440w.jpg)  
**设置Platforms**  
设置设备软件平台，设备平台(Platforms)

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-d6dbee241299c9229b7829c49f0ef9f0_1440w.jpg)  


---

**定义Device Roles**  
定义设备角色，比如access/core/aggregate/border等。

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-8d7e5a128c1fc8297686df8a8d6dbf02_1440w.jpg)  
**添加Device**  
设备设备，添加网络设备

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-cc957de2ac5c9365a02277f2c9ae105c_1440w.jpg)  


---

配置详细信息

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-c3a86fa1926975eb2476157eb5e067b8_1440w.jpg)  
添加设备位置信息

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-41b7d962cef8c668e4bb4364e38737f1_1440w.jpg)  
备注：按照具体物理机房综合布线命名BD、FD等对设备具体位置进行命名，实现物理环境与虚拟管理相对应，方便运维工作人员了解设备具体部署位置



---

**添加Interface**  
在设备列表内直接点击进入具体设备后再 Add Components添加具体接口

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-e24778d0d7397225830b5ead6d3b5500_1440w.jpg)  
选择具体接口类型与速率

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-8e9f48fe4f527739733e6bd6e1e9cd13_1440w.jpg)  
备注：不同厂商不同设备出现在数据中心内，会出现多种Console口与不同的波特率与线缆，通过Netbox设置后可以直观查看接口信息进行连接配置。



---

**添加IP地址与连接**  
在设备内添加完成接口，可以直接设置IP地址与连接端口

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-a861135fd65f34353780ac7055be7677_1440w.jpg)  
设置端口与IP地址的连接，可以通过Trace直接查看端口连接信息，包含有双方连接设备安装位置，对端接口等，使运维工作人员不用查看具体线缆标签便了解数据中心的设备接口连接信息。

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-27ac2911b55275d7412f271bfa1adc24_1440w.jpg)  
全部设备添加完IP地址后可以进入IPAM管理菜单对IP地址进行查看与管理



---

**查看效果**  
在组织机构，立面图内可查看添加的设备与机柜

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-d1a1e329343048fc2e32e3cdc7b0fd67_1440w.jpg)  
备注：将设备添加到机架内，并且可以设置多个机架与数据中心物理环境相对应，在立面图内可以清晰看到不同设备所处机架位置，即使身处数据中心之外，仍对内部环境了如指掌。



---

## 总结  
### Netbox的使用体验  
* Netbox目前不支持中文，导致了很多国内的运维工程师不了解也没有尝试使用；
* Netbox在初始配置时，工作量比较大，需要手工去添加，部分运维工程师嫌麻烦。

但Netbox的理念是强烈建议不要自动导入实时网络状态。在 NetBox 中创建的所有数据

中都应首先经过人工审核，以确保其完整性。

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-8d0a389581a9eccfc764b34949cf9495_1440w.jpg)  
### 关于YAML  
YAML（/ˈjæməl/，尾音类似camel骆驼）是"YAML Ain't a Markup Language"（YAML不是一种[https://baike.baidu.com/item/标记语言?fromModule=lemma\_inlink](https://baike.baidu.com/item/标记语言?fromModule=lemma\_inlink)）的[https://baike.baidu.com/item/递归缩写?fromModule=lemma\_inlink](https://baike.baidu.com/item/递归缩写?fromModule=lemma\_inlink)。YAML的语法和其他高级语言类似，并且可以简单表达清单、散列表，标量等数据形态。它使用空白符号缩进和大量依赖外观的特色，特别适合用来表达或编辑数据结构、各种配置文件、倾印调试内容、文件大纲（例如：许多电子邮件标题格式和YAML非常接近）。尽管它比较适合用来表达层次结构式（hierarchical model）的数据结构，不过也有精致的语法可以表示关系性（relational model）的数据。由于YAML使用空白字符和分行来分隔数据，使得它特别适合用[https://baike.baidu.com/item/grep?fromModule=lemma\_inlink](https://baike.baidu.com/item/grep?fromModule=lemma\_inlink)/[https://baike.baidu.com/item/Python?fromModule=lemma\_inlink](https://baike.baidu.com/item/Python?fromModule=lemma\_inlink)/[https://baike.baidu.com/item/Perl?fromModule=lemma\_inlink](https://baike.baidu.com/item/Perl?fromModule=lemma\_inlink)/[https://baike.baidu.com/item/Ruby?fromModule=lemma\_inlink](https://baike.baidu.com/item/Ruby?fromModule=lemma\_inlink)操作。  
提供一个在线的YAML编辑工具。  
[https://www.bejson.com/validators/yaml\_editor/](https://www.bejson.com/validators/yaml\_editor/)  
如果是用docker安装方式，在netbox-docker有一个名为initializers的目录，其中包含YAML文件。docker-compose up然后，将按顺序执行startup\_scripts目录中的Python脚本，并且在读取initializers下的YAML文件的内容时，数据将被注册到netbox中。

在服务器或交换机上管理的信息主要是以下信息。每个都可以在YAML文件中描述。  
例如：  
已安装的机架(initializers/racks.yml)---xxx  
还有机架角色(rack\_roles.yml)和分组(rack\_groups.yml)---xxx  
已安装设备的名称和位置(initializers/devices.yml)---xxx  
为设备设置的IP地址(initializers/ip\_addresses.yml)---x.x.x.x/xx  
型号名称(initializers/device\_types.yml)--- xxx  
设备供应商(initializers/manufacturers.yml)--- xxx

### 添加ALE网络设备时遇到的小插曲  
对于OS2260-10/P10等交换机在物理环境中可以实现两台交换机安装在同一U内，但是NetBox 使用的机架空间建模方案不支持half-width设备。但是对于OS9907机箱式交换机可以将机箱设置为Parent设备，设置正常尺寸，板卡作为Child设备，尺寸设置为0U添加入内。

### 自动化收集信息  
![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-c543552d557df435aa60700210a1cf75_1440w.jpg)  
在平台内可以添加NAPALM driver，通过自定义使用NAPALM来进行自动化收集设备基础信息如：域名、主机名称、接口列表、型号、版本号、序列号、运行时间、厂商等。目前NAPALM支持的厂家系统有Cisco IOS、Cisco NX-OS、Cisco IOS-XR、Juniper JUNOS、Arista EOS，暂时还没有ALE或其他国产厂家华为、华三等。

![]((20240603)开源运维系统的实践-Netbox安装配置使用_DSC/v2-59e09698d98e6d09a7b606aff5ddb8de_1440w.jpg)  
备注：NAPALM全称为Network Automation and Programmability Abstraction Layer with Multivendor support，Napalm是一种为多厂商的网络设备提供统一API的Python库。

  


### 本人涉及资料来源于日常工作及互联网搜索，已做脱敏处理，如果仍存有涉及侵权内容，请告知。将做删除修改。如有错误，欢迎大家指正。  
