# 开源运维系统的实践-Zabbix结合Grafana配置

 **Author:** [DSC]

 **Link:** [https://zhuanlan.zhihu.com/p/656667716]

**开源运维系统的实践**

**Zabbix结合Grafana配置手册**



---

### 文档说明  
此文档是关于如何将开源软件Zabbix与Grafana相结合，通过Grafana更加丰富的图形列表来美观的展示Zabbix所监控的数据。

### 内容描述  
开源监控系统的实践系列文档-Zabbix结合Grafana配置手册

### 测试环境介绍  


| 所需软件 | 用途 | 下载地址 |
| --- | --- | --- |
| Ubuntu | 20.04 | [https://mirrors.aliyun.com/oldubuntu-releases/releases/20.04.0](https://mirrors.aliyun.com/oldubuntu-releases/releases/20.04.0) |
| Zabbix | 6.0.20 | [https://www.zabbix.com/cn/download](https://www.zabbix.com/cn/download) 或Wget命令下载 |



---

**下载安装Grafana**

安装所需的软件包并下载Grafana存储库签名密钥：


```
sudo apt-get install -y apt-transport-https  
sudo apt-get install -y software-properties-common wget  
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key 
```
添加Grafana的APT存储库，执行以下命令：


```
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | 
sudo tee -a /etc/apt/sources.list.d/grafana.list  
```
更新APT软件包索引：


```
sudo apt update  
```
安装Grafana：


```
sudo apt-get install grafana  
```
启动Grafana服务：


```
sudo systemctl start grafana-server  
```
设置Grafana在系统启动时自动启动：


```
sudo systemctl enable grafana-server  
```
查看Grafana是否正常运行


```
systemctl status grafana-server  
```
![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-7494a3cffa69d7d2e0cd53f468cd9315_1440w.jpg)  


---

**访问Grafana页面下载插件**

访问Grafana的Web界面：[http://localhost:3000/](http://localhost:3000/)，默认用户名密码：admin/admin

![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-dfc84ac57dcab6c50b7639b428486d8b_1440w.jpg)  
下载安装Zabbix插件：


```
grafana-cli plugins list-remote  
systemctl restart grafana-server.service  
```
![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-68f9a04f1787e03e7ee8704461db234d_1440w.jpg)  


---

**开启Zabbix插件**  
主页①Configuration②Plugins  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-e266033e3a465af52f476f25d35b2145_1440w.jpg)  
  
在搜索框内搜寻找到zabbix：  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-b031d6fee36e300ad4e7156caf2b6931_1440w.jpg)  
  




---

  
开启zabbix插件：  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-08678f5cc8e9688ec1d7178347899127_1440w.jpg)  
**配置插件实现Grafana结合Zabbix**  
Configuration-->Data sources-->Add data source  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-7b934ae5f8b8175ce4e1cfcd6dceeb9d_1440w.jpg)  
  




---

  
搜索进入插件配置  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-32c0f47a136d0f894dba3e182c14b4f7_1440w.jpg)  
  
  
配置IP地址：将localhost更换即可，jsonrpc是json格式进行远程过程调用的协议。  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-e507438b9ba0ea51e1bebfae507e7444_1440w.jpg)  
  
部分参数详细信息：



| 参数 | 详细信息 |
| --- | --- |
| Name | 自定义名称 |
| Type | 下拉框中选择zabbix #如果没有安装zabbix插件，此处则没有zabbix选项 |
| URL | [http://zabbix-serverIP/zabbix/api\_jsonrpc.php](http://zabbix-serverIP/zabbix/api\_jsonrpc.php) #zabbixAPI接口地址 |
| Access | 默认Server(Default)，表示grafana直接到Server取数据，brower表示让每个客户端单独获取，一般不用。 |
| Username | 填写zabbix的用户名 |
| Password | 填写zabbix的密码 |
| Trends | 勾选上即可 |

配置Zabbix平台的用户名及密码：  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-cce6e32fc84d0bb769797c540397628a_1440w.jpg)  
  
配置完成后点击下方的save and test进行测试：  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-12c21ead90b2d2b54e5b687f9852c0b5_1440w.jpg)  
**Grafana可视化Zabbix数据**  
New Dashboards-->Add a new panel  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-a52c705efee4a0cda1624be5746b0ea4_1440w.jpg)  
  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-c77095514503411d2560ad4d34e92461_1440w.jpg)  
  
  




---

  
配置Zabbix监控内想要展示的信息：  


![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-97c01e8a25fba14e2b8c5ee1faf3179e_1440w.jpg)  


| 步骤 | 名称 | 详细信息 |
| --- | --- | --- |
| 1 | Data source | 选择数据来源是Zabbix，否则没有信息展示 |
| 2 | Group | 导入Zabbix内ALE模板所新建的组 |
| 3 | Host | 选择Zabbix里监控的主机 |
| 4 | Item tag | 选择监控的类型如CPU，System，Port |
| 5 | Item | 具体监控信息 |
| 6 | Change visualization | 可以更换不同的展示图表 |

**完成效果展示**

在GeneralZabbix Server Dashboard内便可看到展示的监控画面

![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-88f4dd9e1ced628ada17892fb1681e3f_1440w.jpg)  
自定义设置具体展示画面：

![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-09586e650ae3bf407981cae340aa6dbe_1440w.jpg)  
更改与新建步骤相同配置

![]((20230916)开源运维系统的实践-Zabbix结合Grafana配置_DSC/v2-f45b139d46f8e134101c602a88fbf4b6_1440w.jpg)  
### 本人涉及资料来源于日常工作及互联网搜索，已做脱敏处理，如果仍存有涉及侵权内容，请告知。将做删除修改。  
