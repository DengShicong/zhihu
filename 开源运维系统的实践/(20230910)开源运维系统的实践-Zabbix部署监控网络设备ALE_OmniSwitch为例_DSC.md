# 开源运维系统的实践-Zabbix部署监控网络设备(ALE OmniSwitch为例)

 **Author:** [DSC]

 **Link:** [https://zhuanlan.zhihu.com/p/655371738]



---

**前** **言**

### 文档说明  
此文档是介绍如何在Ubuntu内安装与部署Zabbix服务器并为其配置数据库的详细信息，并包括在Zabbix安装完成之后从Github导入网络设备监控模板(以ALE为例)步骤。

### 内容描述  
开源监控系统的实践系列文档-Zabbix部署监控网络设备(ALE为例)

### 测试环境介绍  


| 软件信息 | 版本 | 下载地址 |
| --- | --- | --- |
| Ubuntu | 20.04 | [https://mirrors.aliyun.com/oldubuntu-releases/releases/20.04.0](https://mirrors.aliyun.com/oldubuntu-releases/releases/20.04.0) |
| Zabbix | 6.0.20 | [https://www.zabbix.com/cn/download](https://www.zabbix.com/cn/download) 或使用Wget |
|  |  |  |

1. **安装方式**
* 从源代码安装
* 从二进制包安装
* 从容器中安装

本次测试在Ubuntu内通过wget下载二进制包安装zabbix

1. **安装部署过程**  
安装配置过程流程图：

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-98afab81ec744d5e127e08941952df73_1440w.jpg)  
  




---

1. **安装MySQL数据库**

  


使用命令下载存储库软件包

1. wget -c [https://dev.mysql.com/get/mysql-apt-config\_0.8.10-1\_all.deb](https://dev.mysql.com/get/mysql-apt-config\_0.8.10-1\_all.deb)

使用命令安装安装包，安装过程中提示选择安装版本，默认安装的就是8.0版本，所以直接选择“OK”确认即可；

1. sudo dpkg -i mysql-apt-config\_0.8.10-1\_all.deb

更新软件包信息：

1. sudo apt-get update

安装MySQL，确定版本8.0

1. sudo apt-get install mysql-server

安装后可看到版本

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-f60689808de7bb9c9696a12ca3f96234_1440w.png)  
1. **安装Zabbix及配置数据库**

下载Zabbix的仓库

1. wget [https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release\_6.0-4+ubuntu22.04\_all.deb](https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release\_6.0-4+ubuntu22.04\_all.deb)
2. dpkg -i zabbix-release\_6.0-4+ubuntu22.04\_all.deb
3. apt update

安装Zabbix的三个组件：Zabbix server，Web前端，agent

1. apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent

为Zabbix创建初始数据库（注意！MySQL语法以“;”为标识作为结尾符）

1. # mysql -uroot -p
2. password
3. mysql**>** create database zabbix character set utf8mb4 collate utf8mb4\_bin;
4. mysql**>** create user zabbix@localhost identified by 'password';
5. mysql**>** grant all privileges on zabbix.\* to zabbix@localhost;
6. mysql**>** set global log\_bin\_trust\_function\_creators = 1;
7. mysql**>** quit;



| 1 | 进入数据库 |
| --- | --- |
| 2 | 初始密码 |
| 3 | 创建名为zabbix的数据库并为其设置utf8mb4的编码格式 |
| 4 | 创建数据库用户并设置密码：“password”为密码可自定义 |
| 5 | 为用户授予权限 |
| 6 | 启用二进制日志记录时，可以允许用户创建与更改存储函数，默认为0 |

通过mysql -uzabbix -p 可以查看是否添加用户zabbix

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-76d65ffd7fe9dd8fc448119e188d9fdc_1440w.jpg)  
通过show databases; 可以查看zabbix的数据库已创建

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-8c371ad95411a43772356b1a98a0994a_1440w.jpg)  
导入初始架构和数据，系统将提示您输入新创建的密码。

1. zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --**default**-character-set=utf8mb4 -uzabbix -p zabbix

导入数据库模式后禁用log\_bin\_trust\_function\_creators选项。

1. mysql -uroot -p
2. password
3. mysql> set global log\_bin\_trust\_function\_creators = 0;
4. mysql> quit;

为Zabbix server配置数据库, 编辑配置文件zabbix\_server.conf

1. sudo vi /etc/zabbix/zabbix\_server.conf

找到#DBPassword，另起一行输入 DBPassword=123456，123456是刚刚MySQL新创建的密码，同时可以看到DBUser是zabbix 

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-cb5d11cd998382f3aabe15e6be74acc7_1440w.jpg)  
1. **Zabbix安装及Web端配置**

启动Zabbix server和agent进程，并为它们设置开机自启：

1. service zabbix-server zabbix-agent apache2 restart
2. service zabbix-server zabbix-agent apache2 enable



---

输入ip address show，根据网卡查看对应的IP地址。

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-8e835cf7c1e1de7d8b0358734da30652_1440w.jpg)  
1. **欢迎主界面**  
在浏览器地址输入IP地址进入网页。例如本机测试使用的[http://10.10.10.156/zabbix](http://10.10.10.156/zabbix) ，进入到前端安装的向导程序。

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-fafd84d8c2de7164ddb3ad85b29b4d7e_1440w.jpg)  
  




---

1. **先决条件检查**  
确保满足所有软件先决条件。

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-53d2c2da8bf5cd6f06984323299b20e0_1440w.jpg)  
  




---

1. **配置数据库连通性**  
请在该页面输入连接到数据库所需的详细信息。在创建与数据库的连接前，Zabbix数据库必须先被创建。

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-a7805f3838f343d3d27e18585b244019_1440w.jpg)  
  




---

1. **配置**  
对Zabbix服务器进行命名（可选），选择时区为上海。

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-dd5b47705374467f8c9ba5bb12914291_1440w.jpg)  
1. **预安装总概**  
可以查看配置概要是否正确。

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-ebe1f5ed5f7add40071f6923b47f9c16_1440w.jpg)  
1. **安装**  
安装完成

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-030fa6c6f81b11675916cb818dd94ba6_1440w.jpg)  
  




---

1. **登录**  
Zabbix 前端已准备就绪，默认用户名是Admin，密码zabbix。

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-44f857febba856c7aff5bf3609635613_1440w.jpg)  
  




---

1. **导入监控模板**  
在工作中会有许多相同的或者类似的主机，我们在对它们进行配置的时候会花费我们大量时间去做相同而配置操作，而模板的引入就解决了这个问题。我们可以将那些实体配置写到一个模板中，然后根据需要将模板应用于尽可能多的主机。在Zabbix内自带了众多厂商的监控模板，同时在Github内也有不同的厂商模板可以选择。
1. **模板下载**  
从Github内下载后缀名yaml的模板配置文件,模板地址：  
[https://github.com/zabbix/community-templates/tree/main/Network\_Devices/Alcatel-Lucent\_Enterprise/template\_alcatel-lucent\_enterprise\_omniswitch\_aos\_release\_8.x/6.0](https://github.com/zabbix/community-templates/tree/main/Network\_Devices/Alcatel-Lucent\_Enterprise/template\_alcatel-lucent\_enterprise\_omniswitch\_aos\_release\_8.x/6.0)

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-8c6a881ceb25aa6b1ad04ba90dade5a6_1440w.jpg)  
  
此模板允许通过SNMPv3监控运行AOS 8.x版的Alcatel-Lucent Enterprise OmniSwitch OmniSwitch。该模板也适用于 SNMPv1/v2c。  
附AOS6.x模板地址：  
[https://github.com/zabbix/community-templates/tree/main/Network\_Devices/Alcatel-Lucent\_Enterprise/template\_alcatel-lucent\_enterprise\_omniswitch\_aos\_release\_6.x/6.0](https://github.com/zabbix/community-templates/tree/main/Network\_Devices/Alcatel-Lucent\_Enterprise/template\_alcatel-lucent\_enterprise\_omniswitch\_aos\_release\_6.x/6.0)  




---

1. **模板导入**  
进入Zabbix的Web页面ConfigurationTemplatesImportImport file 将下载的yaml配置文件导入。

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-7cc6b0f471c5090d34cd42edef85f66a_1440w.jpg)  
  
选择下载好的yaml文件  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-5821eeff4d148206604b6cf80da3a4ae_1440w.jpg)  
  




---

  


1. **配置SNMP与发现网络设备**
1. **为交换机开启SNMP**

  


实机测试使用SNMPv2实现Zabbix与交换机连接

交换机端配置

1. aaa authentication snmp local
2. user snmpuser password snmpuser read-writeallno auth//用户名密码自定义(标红)
3. snmp security no-security
4. snmp community-map mode enable
5. snmp community-map public user snmpuser enable//设置团体字，匹配创建的用户名
6. snmp station <station ip> <username> v2 enable
7. **在Zabbix内连接交换机并进行监控**  
新建主机过程配置信息



| Host name | 可自定义名称 |
| --- | --- |
| Visible name | 可见名称，这里设置为OS6860E-24 |
| Templates | 模板导入后会自动新建Group：Templates/Switches并将模板纳入其中 |
| Groups | 选择一个模板组 |
| Interface | 选择添加使用SNMP来连接交换机 |

  
新建host主机  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-cc87ddd2dc5c45a9058fcd5094e02b59_1440w.jpg)  
  




---

  
创建OS6860E-24并设置名称  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-08dcab0bc069b94112b63c4bb1692a1c_1440w.jpg)  
  
添加模板  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-81436f1a45606728698a1d70a975fc41_1440w.jpg)  
  




---

  
InterfaceAddSNMP 配置交换机设备信息  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-95e9a9fc04f65b35692713b42b8ba186_1440w.jpg)  
  
  
配置SNMP信息，设置交换机IP地址，默认端口161，SNMP community引用系统参数  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-f51bec15c03f383a0992bbedfd52a373_1440w.jpg)  
  




---

  
设置 SNMP Community的参数，value值与之前在OS6860E-24设置的团队字相同  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-a22b62f557de565c3d3a7d70a780cba3_1440w.jpg)  
  
完成配置后点击Add/Update，完成新建主机  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-d77bfa64bbb2bd6dcdf2883a9153434d_1440w.png)  
  
我们通过上图可以发现导入模板后可以成功通过SNMP连接ALE交换机，但是Items监控项只有5个，我们通过Discovery自动发现规则来获取ALE交换机的信息并生成监控项  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-b2a52eb71df1e4dc262264bb4f4c2f63_1440w.png)  
  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-593cf309465b610e7d230a34f4296863_1440w.jpg)  
  
查看执行完成后有94项监控项  


![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-406462e9b3c674f75f7f5d2531167401_1440w.png)  
  




---

1. **效果展示及自定义监控图像**

主页仪表盘内设置更改监控图像 MonitoringDashboard

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-e79082882bce8a49fbd4ce5e3583b6de_1440w.jpg)  
  


Type可以选择图像表格或者文字，再在下方选择想要监控的监控项得到图像

![]((20230910)开源运维系统的实践-Zabbix部署监控网络设备ALE_OmniSwitch为例_DSC/v2-dbfedce6d17d3beb2c67f612618ace4c_1440w.jpg)  
  


### 本人涉及资料来源于日常工作及互联网搜索，已做脱敏处理，如果仍存有涉及侵权内容，请告知。将做删除修改。  
