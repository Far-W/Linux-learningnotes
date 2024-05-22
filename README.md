**Centos|Debian|Ubuntu** Command_README 
---
# 第 1 次课	CentOS基础命令
- ## 一、额外备注
    | 软件包名 | 服务名 |
    |:-:|:-:|
    | quagga | zebra.service |
    | vsftpd | vsftpd.service |
    | httpd | httpd.service |
    | dhcp | dhcpd.service |
    | bind | named.service |


- ## 二、挂载盘
    ```
    mkdir /mnt/cdrom
    mount /dev/cdrom /mnt/cdrom
    ```
        ```
     ### 永久挂载光盘操作
    输入：vim /etc/fstab
    ```
    在编辑窗口中添加以下内容：
   UUID=2018-11-25-23-54-16-00  /mnt/cdrom iso9660 defaults 0 0
    ```
    效果：
    ```
    UUID=049299c1-75a0-4016-894d-45ce9945a748 /                       xfs     defaults        0 0
    UUID=2e4b3eb9-c412-480e-aa9e-53ce7c1cb487 /boot                   xfs     defaults        0 0
    UUID=70fc8256-2169-4dce-9008-e24d7b41eed0 swap                    swap    defaults        0 0
    UUID=2018-11-25-23-54-16-00               /mnt/cdrom            iso9660   defaults        0 0

    保存退出
        按“esc”键
        输入“:wq”
    ```

~       

- ## 三、卸载服务
    ```
    rpm –ev <name> [quagga,vsftpd,httpd,dhcp,bind]
    ```
- ## 四、安装服务
    ```
    rpm –ivh <name>
    ```
- ## 五、安装依赖程序
    -   ### 1. 慢慢安装依赖
        ```
        rpm -ivh <name>  # 注意（.so的先不管他）
        ```
        ### 2. 使用yum命令（在Ubuntu和Debian上是apt）
        ```
        yum install <name>  # yum源 etc/yum.repos.d
        yum update <name>
        yum remove <name>
        注：更新所有软件为dnf update -y
        ```
        ### 3. 检查是否安装服务
        ```
        rpm –qa|grep <name>
        ```

- ## 六、设置一些自启动

    - ### 1. 服务自启动
        ```
        systemctl start <name>  # 启动
        systemctl enable <name>  # 自启动
        systemctl disable <name>  # 关闭自启动
        ```
        ### 2. 程序自启动
        ```
        systemctl set-default <name>
        ```

---

# 第 2 次课	CentOS网络基础命令Ⅰ
- ## 一、额外备注
  - |备注|详细
    | :-: | :-: 
    | tar |解压缩的命令
    | rpm | 不能装网络源（yum可以）
    | df –h	| 查看光盘挂载点
    | rpm –qa\|grep | vsftpd	安装

- ## 二、网络相关参数
    ```
    网络相关参数：主机名、IP地址、子网掩码、网关、DNS服务器地址等。
    ```
- ## 三、网络配置方法
	- ### 1. 主机名的配置：
        (1) hostname
        ```
        host<name>ctl set-hostname <name>  # 配置或修改主机名
        ```
        (2) nmcli
        ```
        nmcli general hostname <name>  # 修改
        ```
        (3) nmtui
        ```
        编辑连接 -> 网卡名[ens33] -> 使用TAB移动直接编辑
        ```
        (4) 直接修改配置文件：
        ```
        /etc/hostname
        这种要重启网卡(主机)systemctl restart network.service
        也可简写成：systemctl restart network
        ```
	- ### 2. 地址、网关、子网掩码等其他配置
        (1) 使用网卡的配置文件配置网络（此方法适用于所有Linux版本）（最保险）
        | 名字 | 参数 | 用途 |
        |:-|:-|:-|
        |/etc/sysconfig/network-scripts/ifcfg-ensXXX|-|XXX生成跟mac地址有关
        |TYPE=Ethernet |-|-
        |PROXY_METHOD=none |-|-
        |BROWSER_ONLY=no |-|-
        |BOOTPROTO=static|dhcp 通过DHCP自动获取IP地址 <br>none 静态手动指定 <br>static 静态手动指定|启动的时候采用的协议
        |DEFROUTE=yes |-|-
        |IPV4_FAILURE_FATAL=no |-|-
        |IPV6INIT=yes      |-|-
        |IPV6_AUTOCONF=yes     |-|-
        |IPV6_DEFROUTE=yes      |-|-
        |IPV6_FAILURE_FATAL=no     |-|-
        |IPV6_ADDR_GEN_MODE=stable-privacy     |-|-
        |NAME=ens33|-|设备名
        |UUID=30c1bcff-ab7f-493e-b70e-54f0ba785b88|-|网卡的ID号
        |DEVICE=ens33|-|硬件名
        |ONBOOT=yes|yes 激活<br>no 不激活|是否开机激活网卡|
        |IPADDR=192.168.8.1|-|IP地址
        |NETMASK=255.255.255.0 |-|前缀长度/子网掩码
        |GATEWAY=192.168.8.254|-|网关
        |HWADDR=X\:X\:X\:X\:X\:X|-|MAC地址
        |PREFIX=24 |-|-
        |DNS1=114.114.114.114|-|DNS服务器地址

        (2) 通过nmtui命令进入UI界面
        ```
        编辑连接 -> 网卡名[ens33] -> 使用TAB移动直接编辑
        systemctl restart network.service  # 重启网卡
        ifconfig enXXX  # 查看是否生效
        ```
        (3) 通过nmcli编辑
        ```
        nmcli enXXX
        nmcli  # 直接输入查看
        ```
        ```
        配置各种配置
        nmcli connection modify ens33 ipv4.addresses X.X.X.X/X ipv4.method manual ipv4.gateway X.X.X.X ipv4.dns X.X.X.X
        ```
        ```
        nmcli connection up enXXX  # 设备名/地址立即生效
        nmcli connection show enXXX  # 查看网卡详细信息
        ```
        (4) 通过ip命令 *这个命令只能临时生效，重启就没了*
        ```
        ip addr show  # 展示地址
        ip addr add/del X.X.X/X dev ems33  # 添加地址
        ```
---
# 第 3 次课	Centos网络基础命令Ⅱ
- ## 一、额外备注
    |命令|备注|
    |:---:|---|
    |nmcli<br>nmtui|需要 Network Manager <br> Centos7 自带 8 不一定自带
    |systemctl restart network.server|Centos7 一般不能用|
    |/etc/hosts | DNS 缓存文件
    |/etc/resolv.conf|DNS 服务器地址配置文件
    
- ## 二、Linux的常用配置命令
    ### 1. 查看网卡信息
    - (1) 查看当前网卡活动信息
        ```
        ifconfig  # ens33 网卡  lo 回环  virbr0 虚拟
        ifconfig \<name>  # 查看当前活动网卡信息  
        ifconfig \<name> \<ip>  # 设置网卡的地址
        ```
    ### 2. 查看路由信息
    - (1) 查看路由表
        ```
        route -n
        ```
        (2) 添加/删除默认网关
        ```
        route add/del default gw 网关 IP dev 网卡设备名
        ```
        (3) 添加/删除静态路由
        ```
        route add/del -net 网段/前缀 gw ip dev 设备名
        ```
        (4) 红帽专有命令（图形化）
        ```
        nmtui  # 需要重启网卡
        ```
    ### 3. 网络调试命令
    - (1) 测试网络连通性
        ```
        ping [选项] [IP/域名] 
        ```
        (2) 显示各种与网络相关的状态信息  
        ```
        netstat [options] # 网络连接状态 路由表信息 端口信息（0-1024是保留端口）
        -a 显示所有配置接口
        -l 监听状态
        -r 显示路由表
        -t 显示TCP连接状态
        ```
        (3) 跟踪路由
        ```
        tanceroute [IP/域名]
        ```
        (4) 地址解析
        ```
        nslookup [IP/域名]
        ```
# 第 4 次课  Linux下的路由设置
## 静态准备工作
```
分别创建3张网卡，分为客户机一、客户机二、路由器的网段进行网段规划
```
- ### 步奏1：
```
    使用nmtui命令设置centos网卡的地址掩码等（注意重启（systemctl restart network））
    重启后检查客户机是否能ping通网关
```
- ### 步奏2：分别配置路由转发功能：
```
    vim /etc/sysctl.conf
    net.ipv4.ip_forward = 1
    然后':wq'（保存退出）
    sysctl -p（查看是否生效）
    systemctl restart network（重启生效）
```
- ### 步奏3：
```   
    分别配置静态路由：
    配置静态路由讲解：
    route add -net 目标网段/前缀 gw 转发网关IP dev 网卡设备名
    添加静态路由
    route add -net 192.168.3.0/24 gw 192.168.4.2 dev ens36
    删除静态路由
    route del -net 192.168.3.0/24 gw 192.168.4.2 dev ens36
```
- ### 最后：
```
    查看路由表
    route -n
    检查是否有到对面的路由
    客户机互相ping通
```
## 动态准备工作
```
以上步奏与静态路由相同，不做重复(以下步奏在开启路由转发后进行)
安装quagga服务
```
- ### 步奏1：
```
    安装quagga服务   //安装前请配置yum源
    yum install quagga
    安装完成后
    systemctl start zebra //启动服务
    netstat -tnl  //查看2601端口为监听状态，此端口为zebra运行端口
    systemctl enable zebra //设置开机自启动（课堂非必要配置）
    配置文件在/etc/quagga/ripd.conf
    配置文件讲解：
    使用vim  /etc/quagga/ripd.conf
    具体内容：
    ```
    router rip
    network 192.168.x.0/24(第一张网卡)
    network 192.168.x.0/24(第二张网卡)
    version 2(使用ripv2协议)
    interface ens33 //第一张网卡
    ip rip authentication mode text
    ip rip authentication password admin523
    interface ens36 //第二张网卡
    ip rip authentication mode text
    ip rip authentication password admin523
    ```

```
- ### 步奏2：启动rip服务，并且查看路由表：
```
systemctl stop firewalld  //关闭防火墙
systemctl start ripd  //启动rip服务
netstat -tnl  //查看2601端口为监听状态，此端口为zebra运行端口
ripd -d  //查看ripd服务是否正常

-- 客户机互ping
```

# samba服务器管理与配置

    ```
    扩展知识
    cifs（网上邻居）  //只支持windows
    ftp（文件传输协议）
    nfs/cifs   linux/unix 
    smb(协议)
    samba service （服务器）//可跨系统共享文件
    samba功能：
    共享文件与打印机
    提供用户登陆时的身份验证
    可以进行windows的主机名解析
    可以进行设备的共享
    samba中的两个进程守护
    ambd：作用处理SMB请求包，负责用户验证，文件打印机共享
    nmbd：管理工作组，负责主机名解析和浏览共享  // 对外发布samba可提供的服务
    ```
-  ### 准备工作（安装服务）
```
samba配置文件
samba：
/etc/samba/smb.conf   //主配置文件
samba/smbpasswd          用户映射文件    //虚拟用户文件
```
    
- # less /etc/samba/smb.conf    //配置文件讲解
```

    [global]  //全局变量配置
    security = user     //用户验证/访问模式
    share               //共享不需要登陆密码
    user                //使用本地数据库验证用户身份(默认)
    server              //使用第三方主机验证
    domain              //使用域控制器的口令数据库验证
    ads                 //把系统配置为活动目录网络上的域名
    passdb backend = tdbsam    //数据库验证格式
    tdbsam              //使用本地数据库验证用户身份
    ldapsam             //使用LDAP服务器验证用户身份
    smbpasswd           //使用smbpasswd文件验证用户身份

    [homes]  //用户宿主共享
    valid user = %S     //共享
    %s 共享所有
    @group 代表某个组的用户登陆有效
    用户名  代表只有这个用户登陆才有效


    [printers]  //打印机共享
    path = /var/lib/samba/printers  //打印机共享路径
    browseable = yes/no   //浏览权限
    writable = yes/no    //读写权限

    [print$]  自定义//设置其他目录共享 
    create mask = 070   //创建的文件的初始权限
 ```

        
- # 具体配置过程
```
开始之前请完成静态路由的路由转发部分以及IP地址的部署，可参考我丢配置，如下
VMnet2   centOS中：ens33   192.168.2.1/24    网关：192.168.2.254。
                  客户机:192.168.2.2/24  网关：192.168.2.254
                  客户机还需要关闭防火墙
                   开启lan认证：win+r    输入“secpol.msc”
                  选择“本地策略”——“安全选项”——“网络安全：LAN Manager  身份验证级别”——然后点击属性，选择”发送
LM和NTLM-若协商……“——应用，确定。
                  
以下是完成基本配置后需要做的：

nmtui
[root@localhost ~]# systemctl restart network
[root@localhost ~]# ifconfig ens33
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.2.1  netmask 255.255.255.0  broadcast 192.168.2.255
        //这里的192.168.2.1。请记住这个ip，待会配置完成会有用

        inet6 fe80::b335:a54a:2c84:8ac1  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:c8:5a:93  txqueuelen 1000  (Ethernet)
        RX packets 68  bytes 8695 (8.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 68  bytes 9003 (8.7 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[root@localhost ~]# mkdir -p /var/samba/shared
[root@localhost ~]# vim /var/samba/shared/11.txt
在文件中输入任意能容
esc键，键入":wq"保存退出
[root@localhost ~]# useradd wx
[root@localhost ~]# passwd wx
更改用户 wx 的密码 。
新的 密码：
无效的密码： 密码未通过字典检查 - 它基于字典单词
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
[root@localhost ~]# cp /etc/sa
samba/  sane.d/ sasl2/  
[root@localhost ~]# cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
[root@localhost ~]# vim /etc/samba/smb.conf
其中的更改内容在此页面最下方


[root@localhost ~]# smbpasswd -a wx
                                      admin523（密码可自定义）
                                      admin523

[root@localhost ~]#systemctl stop firwalld
[root@localhost ~]#systemctl start smb.service
[root@localhost ~]#systemctl start nmb.service
[root@localhost ~]#systemctl restart smb.service
[root@localhost ~]#systemctl restart nmb.service


到这里就完成了，可以去客户机访问你刚刚记住的ip


[global]
        workgroup = workgroup
        security = user
        netbios name=MYSERVER
        passdb backend = tdbsam
        encrypt passwords=yes
        ntlm auth=yes
        printing = cups
        printcap name = cups
        load printers = yes
        cups options = raw

[homes]
        comment = Home Directories
        valid users = %S
        browseable = yes
        read only = yes

[printers]
        comment = All Printers
        path = /var/tmp
        printable = Yes
        create mask = 0600
        browseable = No

[wxshare$]
        comment = Pubic stuff
        path = /var/samba/shared
        writeable=yes
        browseable=yes
        create mask = 0664
                                                              36,2-9       底端

```
 
# 第 5 次课  DHCP服务器配置
 ## 基础理论
- ### 1. 基础知识
```
- 概述
    DHCP服务器：
    DHCP起源BOOTP   //无盘工作站
    DHCP  动态主机配置协议   //可以为客户端动态分配ip

    DHCP客户机   discovers   //发现    广播
    DHCP服务器   offers     //提供 地址、掩码、网关、dns服务器    广播
    DHCP客户机   requests    //请求   使用第一响应的服务分配地址   广播
    DHCP服务器   ack         //确认   单播
- 工作原理
    IP地址租约和更新
    在租期到期之前，客户端会向服务器发送两次DHCP请求，用以更新租约
    DHCP服务器会发送DHCPACK给客户端，表示租约更新成功或失败，失败则重新请求地址
```
- ## 2. DHCP安装包
dhcp-4.2.5-9.el7.x86_64.rpm
dhcp-libs-4.2.5-9.el7.x86_64.rpm   //DHCP服务器和客户端使用的共享库
dhcp-common-4.2.5-9.el7.x86_64.rpm   //DHCP服务器的配置文件
- ## 3. 配置文件

模板文件：/usr/share/doc/dhcp-xx/dhcpd.comf.example
-
/etc/dhcp/dhcpd.conf
-
dhcpd.conf
-
以下是配置内容
-
```
ddns-update-style none;
log-facility local7;
default-lease-time 600;
max-lease-time 3600;
subnet 192.168.1.0 netmask 255.255.255.0{
  range 192.168.1.20 192.168.1.130;
  option domain-name-servers 192.168.1.1;
  option domain-name "wx.com";
  option routers 192.168.1.1;
  option broadcast-address 192.168.1.255;
}
host dhcpclient{
  hardware ethernet 00:0c:29:5a:80:62;
  fix-address 192.168.1.100;
}

```
~          

 ## DHCP中继代理
 - ###  
 -
 开启所有DHCP中继
 -
 dhcrelay DHCP
 -
开启指定网卡的dhcp中继
-
 dhcrelay -i 网卡




# 第 6 次课  DNS服务器配置
- ## DNS概述：
```
dns :域名系统
    作用：
        域名解析：
        负载均衡：
        反向解析：    
    DNS服务器： 
        递归查询：当服务器无法解析时，会让其他DNS服务器进行查询    //客户机与服务器
        迭代查询：当服务器查询地址时，会向其他DNS服务器进行查询，直到找到为止   //服务器与服务器
            补充:hosts文件（缓存文件）    //域名——IP地址映射
    DNS解析：
        正向解析：域名——IP   // 域名解析IP	
        逆向解析：IP——域名   // IP解析域名
    DNS服务器分类：
        主DNS服务器：为区域提供DNS服务
        从DNS服务器：DNS服务查询工作
        高速缓存DNS：使用缓存的DNS信息进行域名装换，（速度较快）
```
DNS服务安装
```
bind-x.x.x.     //DNS服务主程序包
bind-libs-x.x.x.x      //DNS服务主程序包的依赖包，库文件
bind-utils-x.x.x.x      //DNS服务主程序包的依赖包，工具
bind-chroot-x.x.x.x
bind-devel-x.x.x.x
```
- ## DNS配置文件

- 位置：/etc/named.conf     //主配置文件
```
options {       //全局配置
    listen-on port 53 { any; };   //监听的IP地址的端口：any为所有：也可指定ip
    allow-query { any; };     //允许查询的网段：any为所有,可指定ip，localhost为本地
    dnssec-enable yes;     //是否允许DNSSEC
    dnssec-validation yes;     //是否允许DNSSEC验证
    recursion yes;     //是否允许递归查询
} 


logging {      //日志配置
    channels {     //日志通道
        local0 {     //日志通道名称
            file "/var/log/named/named.run";   文件路径	
            severity dynamic;   //日志级别状态
        }
    }

}     


zone "." IN {    //根区域，定义解析区域      这部分易出错，建议放到扩展配置文件中
    type hint;     //类型
    file "named.ca";     //文件路径
} ;
include "/etc/named.rfc1912.zones";     //引入其他区域配置文件
include "/etc/named.root.key";     //引入其他区域配置文件

根域//定义根域的区域文件为named.ca，文件的位置为/var/named/named.ca。在拓展配置中会使用到此文件
```
- ## 配置拓展区域文件
-区域文件一般放在/var/named/ 中，一般每个区域需要放俩个区域文件，即正/反向区域文件，一般有六种资源记录
```
SOA              //资源记录起始授权记录，一个区域只有一个
NA               //资源记录名称服务器资源记录
A                //正向解析资源记录
PTR              //反向解析资源记录
MX               //邮件资源记录
CNAME            //别名资源记录
```
-区域类型：
```
master              //主域
slave               //辅助域
stub                //只复制ns
forward             //转发域
hint                //根域
delegation-only     //用于强制区域的delegation.ly状态
```
-位置：/etc/named.rfc1912.zones  //模板文件位置
```
    zone "." IN {    //根区域，定义解析区域
        type hint;     //类型
        file "named.ca";     //文件路径
        allow-update { none; };     //允许更新，此选项可不写
    } ;
    zone "jqe.com" IN {    //定义解析区域
        type master;     //类型
        file "jqe.com.zone";     //文件路径
        allow-update { none; };     //允许更新  
    }
```
-配置文件的检测工具
```
named -checkzone   域名  区域文件
eg.namde-checkzone  JQE.com.zone
```
## 配置named步骤
cd /var/named/nam   

###  完整配置过程代写
```
有时间再搞这里，现在没精力，
```



# 第 7 次课  Web服务器配置
## 一，概述
```
-万维网三要素：http、html、url
-http协议：超文本传输协议，规定了浏览器和万维网服务器之间互相通信的规则
-html：超文本标记语言，规定了网页的标签
-url：统一资源定位符，规定了资源在互联网中的地址
```
### 1.1 Web
```
-web服务器：接收客户端的请求，将请求的资源返回给客户端
  动态两种架构：
    LAMP：linux+apache+mysql+php
        L：linux操作系统
        A：apache服务器
        M：mysql数据库
        P：php解释器(编程语言)
    全套微软：
        Windows服务器：
        IIS：服务器
        MSSQL：数据库
        ASP：编程语言
```
### 1.2 apache服务器安装
```
rpm -qa|grep httpd           //查看是否安装了apache服务器
    httpd-tools-2.4.6-88.el7.centos.x86_64    //工具包
    httpd-2.4.6-88.el7.centos.x86_64          //服务包
    httpd-devel-2.4.6-88.el7.centos.x86_64   //开发包

yum -y install httpd         //安装apache服务器
systemctl start httpd          //启动apache服务器

```
###  1.3 apache相关目录与配置文件
```
-配置文件：/etc/httpd/conf/httpd.conf

-主配置文件：/etc/httpd/conf/httpd.conf

-虚拟主机配置文件目录：/etc/httpd/conf.d/      //这是目录，里面存放附加配置文件
-conf.d目录中，所有的.conf文件，全部包含进主配置文件，用户可以在不修改主配置文件的情况下，通过修改conf.d目录中的配置文件，来达到配置虚拟主机的目的。

-日志文件：/var/log/httpd/access_log

-网站发布默认文档根目录：/var/www/html/    
```
### 1.4 配置文件详解：
```
1、global                          //全局配置
        serverRoot                        //服务器根目录
        listen                            //监听端口
        pidFile                           //进程号文件
        user                              //运行用户
        group                             //运行组
        serverAdmin                       //管理员邮箱
        serverName                        //主机名
        <   目录>                         //目录访问控制
2、main server                     //主服务配置
        DocumentRoot                      //网站文档根目录
        DirectoryIndex                    //首页名称
        AddDefaultCharset                  //编码格式

3、virtual host                    //虚拟主机配置
```

### 2 配置需要更改的文件
```
cd /home/wangxin/public_html/      //在这个文件路径中存放.html文件以及辅助文件，如.css .js等

vim XX.html    //编辑这个文件

[root@localhost ~]# vim /etc/named.conf                     //这里编辑dns服务
更改三个“any”

[root@localhost ~]# vim /etc/named.rfc1912.zones            //dns服务编辑
zone "wangxin.jw4b.com" IN {
        type master;
        file "wangxin.com.zone";      //域名反向解析的文件名，通常在  /var/named/当中，其创建过程为：cp named.localhost  wangxin.com.zone//
        allow-update { none; };   
};
编辑如上的域名，以便使用域名访问web个人网页  上图为“www.wangxin.jw4b.com"

```
### 3 配置httpd服务过程
--------------------------------------------------------------------
1,更改网卡设置
2，systemctl stop firewalld.service     //关闭防火墙
---
2，cd /home/wenjianm/zhiwenjianming/htmlwenjian.html或/var/www/html/wenjian.html
wenjianming.html     //进入这个文件可进行网页内容的撰写
---
3，vim /etc/httpd/conf/httpd.conf        //编辑主配置文件  
---
4，systemctl restart httpd.service        //重启httpd服务
---

---------------------------------------------------------------------

```
[root@localhost ~]# mkdir /var/web1
[root@localhost ~]# mkdir /var/web2
[root@localhost ~]# echo "this is my web of host 192.168.1.1">> /var/web1/index.html
[root@localhost ~]# vim /var/web2/index.html
[root@localhost ~]# chmod -R 777 /var/web1
[root@localhost ~]# chmod 777 /var/web1/index.html 
[root@localhost ~]# chmod -R 777 /var/web2
[root@localhost ~]# chmod 777 /var/web2/index.html 
[root@localhost ~]# mkdir -p /var/log/httpd/www.web1.com 
[root@localhost ~]# mkdir -p /var/log/httpd/www.web2.com 
[root@localhost ~]# touch /var/log/httpd/www.web1.com/error.log
[root@localhost ~]# touch /var/log/httpd/www.web2.com/error.log
[root@localhost ~]# vim /etc/httpd/conf.d/ipvh.conf
            --------------------------------------
            <VirtualHost 192.168.1.1>
            ServerAdmin webmaster@web1.com
            DocumentRoot /var/web1
            ServerName www.web1.com
            ErrorLog /var/log/httpd/www.web1.com/error.log
            </virtualHost>
            <VirtualHost 192.168.1.2>
            ServerAdmin webmaster@web2.com
            DocumentRoot /var/web2
            ServerName www.web2.com
            ErrorLog /var/log/httpd/www.web2.com/error.log
            </virtualHost>

            --------------------------------------

[root@localhost ~]# vim  /etc/selinux/config     //并不是每一个都要改，但还是建议用cat命令检查一下
            --------------------------------------
            SELINUX=disabled      //此处为更改过后的样式,更改完成后需要重启服务器。shutdown -r now
            --------------------------------------
[root@localhost ~]# systemctl restart httpd
[root@localhost ~]# vim /etc/httpd/conf/httpd.conf
            ------------------------------------------
            ServerName 192.168.1.1

            <Directory />
                AllowOverride none
                # Require all denied
            </Directory>
            ------------------------------------------

[root@localhost ~]# systemctl restart httpd
```

承接上面的操作，接着：
```

 nmtui
[root@localhost ~]# systemctl restart network
[root@localhost ~]# vim /etc/named.conf
[root@localhost ~]# vim /etc/named.rfc1912.zones
[root@localhost ~]# cd /var/named/
[root@localhost named]# cp named.localhost b.com.zone
[root@localhost named]# vim b.com.zone 
        -------------------------------------------
        $TTL 1D
		@	IN SOA	b.com. root.b.com. (
							0	; serial
							1D	; refresh
							1H	; retry
							1W	; expire
							3H )	; minimum
		@ IN NS dns.b.com.
	    dns IN A 192.168.1.1
		www IN A 192.168.1.1
        -------------------------------------------

[root@localhost named]# cp named.localhos s.com.zone
[root@localhost named]# vim s.com.zone 
        -------------------------------------------
        $TTL 1D
		@	IN SOA	s.com. root.s.com. (
							0	; serial
							1D	; refresh
							1H	; retry
							1W	; expire
							3H )	; minimum
		@ IN NS dns.s.com.
		dns IN A 192.168.1.1
		www IN A 192.168.1.1
        -------------------------------------------

[root@localhost named]# chown root:named s.com.zone         
[root@localhost named]# chown root:named b.com.zone 
[root@localhost named]# mkdir -p /var/webs/s
[root@localhost named]# mkdir -p /var/webs/b
[root@localhost named]# echo "Welcom to www.a.com">>/var/webs/a/index.html
[root@localhost named]# echo "Welcom to www.c.com">>/var/webs/c/index.html
[root@localhost named]# chmod -R 777 /var/webs/s
[root@localhost named]# chmod -R 777 /var/webs/b
[root@localhost named]# chmod  777 /var/webs/b/index.html 
[root@localhost named]# chmod  777 /var/webs/s/index.html   
[root@localhost named]# vim /etc/httpd/conf.d/ymvh.conf
		--------------------------------------------------
        <VirtualHost 192.168.1.1>
   			ServerAdmin webmaster@s.com
    		DocumentRoot /var/webs/s
    		ServerName www.s.com
    		directoryindex index.html
		</VirtualHost>
		<VirtualHost 192.168.1.1>
    		ServerAdmin webmaster@b.com
    		DocumentRoot /var/webs/b
    		ServerName www.b.com
    		directoryindex index.html
		</VirtualHost>
		--------------------------------------------------
[root@localhost named]# systemctl restart httpd
[root@localhost named]# systemctl restart named

```




# 第八次ftp服务器理论到部署
## 理论
```
ftp工作原理：
    双端口：20 （数据）  21（控制端口）在监听状态下，保持一直开启
    主动模式：
        客户端发送PORT命令，服务器回送一个端口，客户端连接该端口
    被动模式：
        客户端发送PORT命令，服务器回送一个端口，客户端连接该端口
    ftp三种登录方式：
        匿名登录：
            用户名：anonymous
            密码：空
        本地用户登录：
            用户名：本地用户
            密码：本地用户密码
        虚拟用户登录：
            用户名：虚拟用户
            密码：虚拟用户密码
        FTP软件：
            vsftpd        //RH内置该软件
            proftpd       
            pureftpd
            Wu-FTP
        安装ftp服务器软件包：
            yum -y install vsftpd

```
- 配置步骤及相应文件
---
```
    /etc/vsftpd/vsftpd.conf        //主配置文件
    ---
    /etc/pam.d/vsftpd        //   虚拟账户
    ---
    /var/ftp/                 //匿名用户主目录
    ---
    /etc/vsftpd/ftpusers        //禁止使用的用户列表（黑名单）
    ---
    /etc/vsftpd/user_list        //禁止或允许使用的用户列表
         --- 文件中的用户不能访问ftp
         userlist_enable=yes
         userlist_deny=yes
         --- 文件中的用户能访问ftp
         userlist_enable=yes
         userlist_deny=no
    ftp地址形式：
        ftp://用户名:密码@ftp server(域名)/端口号/路径/文件名。


```

- 具体配置步骤

```
/etc/vsftpd/vsftpd.conf
----------------------------------------------------
    anonymous_enable=YES                       //是否启用匿名用户
    write_enable=YES                           //是否启用写权限，基于全局
    anon_upload_enable=YES                     //是否允许匿名用户上传文件
    anon_umask=022                             //匿名用户创建文件时的权限掩码
    anon_mkdir_write_enable=YES                //是否允许匿名用户创建目录并设置写权限
    anom_other_wtite_enabke=YES                //是否允许匿名用户对其他文件设置写权限
    dirmessage_enable=YES                      //是否在目录列表中显示消息
    xferlog_enable=YES                         //是否记录文件传输日志
    connect_from_port_20=YES                   //是否允许来自20端口的连接
    xferlog_std_format=YES                     //是否使用标准日志格式
    ftpd_banner=Welcome to myFTP service.      //设置登录服务器显示的信息
    listen=NO                                  //是否监听IPv4连接
    listen_ipv6=YES                            //是否监听IPv6连接
    chroot_list_enable=YES                     //是否限制用户只能访问指定文件夹
    chroot_list_file=/etc/vsftpd/chroot_list   //指定限制用户只能访问的文件夹列表文件
    chroot_local_user=YES                      //是否允许本地用户访问限制的文件夹
    pam_service_name=vsftpd                    //设置PAM服务名称
    userlist_enable=YES                        //是否启用用户列表
    tcp_wrappers=YES                           //是否启用TCP包装器

----------------------------------------------------


```
        
    `

    pam_service_name=vsftpd
    userlist_enable=YES
    tcp_wrappers=YES
---------------------------------------------------


```

## 部署
```
[root@localhost ~]# yum -y install vsftpd
[root@localhost ~]# systemctl restart vsftpd


```



```




# 第九次Nginx Web服务器理论到部署
## 理论
```
    Nginx Web:
      优 势：防火墙、防DDOS、防CC攻击、防SQL注入、防XSS攻击、防HTTP劫持、防数据篡改、防数据丢失、防数据被窃取
      具有高性能、高并发、高可靠性的Web服务器软件。
     
      缺点：不支持动态网站、不支持PHP、不支持JSP

    Nginx Wbe 入门简介：
    Nginx是一个开源，支持高性能，高并发的Web服务器软件。
    Nginx是一款轻量级的Web服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器。
    Nginx是由Igor Sysoev为俄罗斯访问量第二的Rambler.ru站点开发的，第一个公开版本0.1.0发布于2004年10月4日。  
    
```

- 配置步骤及相应文件
```


```

- 具体配置步骤
```


```
