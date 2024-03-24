**Centos|Debian|Ubuntu** Command_README *Farstriders*
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
    很简单的东西我也不知道为什么要用一个节来表示:)
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
 


    