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
subnet 192.168.175.0 netmask 255.255.255.0{
  range 192.168.175.110 192.168.175.130;
  option domain-name-servers 202.102.192.68;
  option domain-name "JQE.com";
  option routers 192.168.175.254;
  option broadcast-address 192.168.175.254;
}
host dhcpclient{
  hardware ethernet 00:0c:29:8e:d6:58;
  fix-address 192.168.175.70;
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
        递归查询：
        迭代查询：
            补充:hosts文件（缓存文件）    //域名——IP地址映射
    DNS服务器分类：
        主DNS服务器：为区域提供DNS服务
        从DNS服务器：DNS服务查询工作
        高速缓存DNS：使用缓存的DNS信息进行域名装换，（速度较快）
```


