# 虚拟网卡

本文档仅介绍虚拟网卡、辅助IP等与产品页相关功能操作。

## 名词解释

* 默认网卡：主机开启网卡功能时由系统默认创建的网卡，原本绑定在云主机的EIP和防火墙都将落在默认网卡上。

* 自定义网卡：控制台手动创建的网卡，可与云主机弹性绑定。

* 辅助IP：控制台手动申请辅助IP，与主IP配合使用，可与云主机弹性绑定，可申请数量与云主机配置有关。

  ![UNI001](../images/UNI001.png)

## 创建网卡

登录控制台，【全部产品】中选择[虚拟网卡](https://console.ucloud.cn/vpc/vnic)，点击【创建虚拟网卡】

![创建虚拟网卡1](../images/创建虚拟网卡1.png)

![创建虚拟网卡2](../images/创建虚拟网卡2.png)

说明：

- 新建网卡自动绑定默认web防火墙。
- 自定义网卡独享配置，如资源名称备注、业务组、弹性IP、防火墙配置等，与主机无关，可与云主机弹性绑定。
- 云主机的网络配置自动落到默认网卡，默认网卡与云主机强绑定，与云主机的生命周期一致。

## 使用网卡

新创建的网卡是未绑定的外网弹性IP的，需要在创建完网卡后再行绑定。同时，可以在虚拟网卡列表页或详情页对网卡资源进行名称更改、绑定主机等操作。

> 自定义网卡绑定云主机后，需要在云主机上配置网卡信息和策略路由到系统（默认网卡无需配置）。

- 列表页操作![列表页操作](../images/列表页操作.png)

- 详情页操作![列表页详情](../images/列表页详情.png)

- 绑定网卡到云主机 ![绑定云主机]

  ![虚拟网卡绑定云主机](../images/虚拟网卡绑定云主机.png)

说明：

- 列表页点击点击【资源名称】或者在操作列点击【详情】即可进入资源的详情页面。
- 在控制台右上角，支持按需展示网卡信息列、下载网卡资源列表、刷新网卡资源列表信息、查阅文档等操作。

## 网卡配置指南

云主机默认网卡无需配置，但对应的辅助IP需要进行配置。

### CentOS7配置指南

现有主机的三张网卡配置如下，且两张自定义网卡已绑定到云主机。

```
eth0（默认网卡）
(主IP) 10.42.108.166
(辅助IP) 10.42.107.2
eth1（创建的自定义网卡）
(主IP) 10.42.71.137
(辅助IP) 10.42.71.3
eth2（创建的自定义网卡）
(主IP) 10.42.175.116
(辅助IP) 10.42.175.3
```

#### 第一步：关闭RPF

*临时关闭*

修改/proc/sys/net/ipv4/conf/all/rp_filter值：
```
echo 0 > /proc/sys/net/ipv4/conf/all/rp_filter
```
重启网络服务
```
service network restart
```

*永久关闭*

编辑/etc/sysctl.conf文件， 修改net.ipv4.conf.all.rp_filter值为0，然后重启服务器


#### 第二步：配置自定义网卡

*配置eth1*

```
# ifconfig eth1 10.42.71.137 netmask 255.255.0.0
# ifconfig eth1 mtu 1454
# echo "101 net_101 " >> /etc/iproute2/rt_tables
# ip route add default via 10.42.0.1 dev eth1 src 10.42.71.137 table net_101
# ip rule add from 10.42.71.137 table net_101
```

*配置eth2*

```
# ifconfig eth2 10.42.175.116 netmask 255.255.0.0
# ifconfig eth2 mtu 1454
# echo "102 net_102 " >> /etc/iproute2/rt_tables
# ip route add default via 10.42.0.1 dev eth2 src 10.42.175.116 table net_102
# ip rule add from 10.42.175.116 table net_102
```

*配置持久化*

eth1和eth2的网卡写配置文件
```
创建配置文件
# cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth1
# cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth2

修改文件中的
- DEVICE=虚拟网卡的网卡名
- HWADDR=虚拟网卡的MAC地址
- IPADDR=虚拟网卡的IP地址
```

策略路由写配置文件

```
# cat /etc/sysconfig/network-scripts/route-eth1

default via 10.42.0.1 dev eth1 src 10.42.71.137 table net_101

# cat /etc/sysconfig/network-scripts/rule-eth1

from 10.42.71.137 table net_101



# cat /etc/sysconfig/network-scripts/route-eth2

default via 10.42.0.1 dev eth2 src 10.42.175.116 table net_102

# cat /etc/sysconfig/network-scripts/rule-eth2

from 10.42.175.116 table net_102
```

#### 第三步：配置辅助IP

```
ip addr add 10.42.107.2 dev eth0
ip addr add 10.42.71.3 dev eth1
ip addr add 10.42.175.3 dev eth2
将IP地址替换成待绑定辅助IP地址，配置默认网卡辅助IP只需将网卡名称改成eth0，以此类推
```

添加的虚拟网卡主IP和辅助IP均可以ping通即配置完成;

#### 第四步：辅助IP绑定EIP后，配置策略路由步骤

新建策略路由表

```
echo '101 net_101' >> /etc/iproute2/rt_tables
```

配置策略匹配规则

```
ip rule add from X.X.X.X（辅助IP） table net_101
ip rule add from X.X.X.X（辅助IP） table net_102
```

配置策略路由

```
ip route add default via X.X.X.X（网关IP） dev eth1 table net_101
ip route add default via X.X.X.X（网关IP） dev eth2 table net_102
```



### CentOS 8配置指南

按照配额，购买一台2C2G的云主机，可以绑定2张虚拟网卡，每张虚拟网卡可以申请6个辅助IP。

现有主机的2张网卡配置如下，且一张自定义网卡已绑定到云主机。

```
eth0（默认网卡）
(主IP) 10.40.121.96
(辅助IP) 10.40.4.124
(辅助IP) 10.40.91.199
...
(辅助IP) 10.40.47.171
eth1（创建的自定义网卡）
(主IP) 10.40.54.131
(辅助IP) 10.40.33.188
(辅助IP) 10.40.134.89
...
(辅助IP) 10.40.44.173
```

#### 第一步：关闭RPF

*临时关闭*

修改/proc/sys/net/ipv4/conf/all/rp_filter值：

```
echo 0 > /proc/sys/net/ipv4/conf/all/rp_filter
```

重启网络服务

```
nmcli c reload
```

#### 第二步：配置自定义网卡

```
# cp -f /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth1
修改文件中的

- DEVICE=虚拟网卡的网卡名
- HWADDR=虚拟网卡的MAC地址
- IPADDR=虚拟网卡的主IP地址

示例：
DEVICE=eth1
HWADDR=52:54:00:a7:90:c8
IPADDR=10.13.72.245
```

#### 第三步：配置策略路由

```
# nmcli c
NAME         UUID                                  TYPE      DEVICE
System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
System eth1  9c92fad9-6ecb-3e6c-eb4d-8a47c6f50c04  ethernet  eth1

# nmcli c show System\ eth1 | grep -E 'ipv4.route-table|ipv4.routing-rules'
ipv4.route-table:                       0 (unspec)
ipv4.routing-rules:                     --

# nmcli c modify System\ eth1 +ipv4.route-table 101
# nmcli c modify System\ eth1 +ipv4.routing-rules "priority 32765 from 10.13.72.245 table 101"
# nmcli c show System\ eth1 | grep -E 'ipv4.route-table|ipv4.routing-rules'
ipv4.route-table:                       101
ipv4.routing-rules:                     priority 32765 from 10.13.72.245 table 101

#nmcli c reload
#nmcli c up System\ eth1
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/...)
#ip rule
...
32765:   from 10.13.72.245 lookup 101
...
```

#### 第四步：配置辅助IP

```
# nmcli c modify System\ eth1 +ipv4.addresses 10.13.100.199/16
# nmcli c show System\ eth1 | grep ipv4.addresses
ipv4.addresses:                         10.13.72.245/16, 10.13.100.199/16

# nmcli c modify System\ eth1 +ipv4.routing-rules "priority 32764 from 10.13.100.199 table 101"
# nmcli c show System\ eth1 | grep ipv4.routing-rules
ipv4.routing-rules:                     priority 32765 from 10.13.72.245 table 101, priority 32764 from 10.13.100.199 table 101

# nmcli c reload
# nmcli c up System\ eth1

#ip rule
...
32764:   from 10.13.100.199 lookup 101
32765:   from 10.13.72.245 lookup 101
...
```

添加的虚拟网卡主IP和辅助IP均可以ping通即配置完成

#### 第五步：辅助IP绑定EIP后，配置策略路由步骤

新建策略路由表

```
echo '101 net_101' >> /etc/iproute2/rt_tables
```

配置策略匹配规则

```
ip rule add from X.X.X.X（辅助IP） table net_101
```

配置策略路由

```
ip route add default via X.X.X.X（网关IP） dev eth1 table net_101
```



### Windows配置指南

标准镜像默认开通DHCP，无需多余配置。

若DHCP已关闭，手动配置方式如下：

1、进入到Windows系统的“网络和共享中心”，如果是已开启了DHCP的系统，可以看到绑定的网卡。

![image](/images/guide/uni6.png)

2、点击网络，依次点击“属性”- 双击“Internet Protocol Version 4（TCP/IPv4）”- 选择 “使用下面的IP地址”，按照实际配置填写IP和DNS信息。

![image](/images/guide/uni5.png)

3、验证：使用同VPC下机器ping绑定的自定义网卡内网IP地址，可以ping通说明配置完成。



### ubuntu20.04配置指南

**1、关闭RPF，重启网络服务**

临时关闭

```
echo 0 > /proc/sys/net/ipv4/conf/all/rp_filter

sudo apt-get install network-manager (安装network-manager工具)

sudo service network-manager restart
```

***永久关闭***

编辑/etc/sysctl.conf文件， 修改net.ipv4.conf.all.rp_filter值为0，然后重启服务器

**2、配置新绑定的虚拟网卡**

假设新绑定的网卡为eth1，以下操作均基于此进行

sudo ifconfig eth1 up

编辑 /etc/netplan/50-cloud-init.yaml

vim /etc/netplan/50-cloud-init.yaml，新绑定的网卡配置示例如下，根据实际进行修改

![](../images/网卡配置辅助IP.png)

sudo netplan apply

**3、临时配置策略路由**（主机重启会失效）**

```
 ip route add default via 10.0.0.1 dev eth1 table 2000

 ip rule add from 10.0.0.222 table 2000
```

**4、临时配置辅助IP(使用辅助IP时进行设置)**

1）将辅助IP绑定到对应网卡上(此处为eth1)，临时配置(主机重启会失效)

```
 ip addr add 10.0.0.101/24 dev eth1 #使用辅助IP时设置

 ip addr add 10.0.0.102/24 dev eth1 #使用辅助IP时设置
```

2）辅助IP配置策略路由，临时配置(主机重启会失效)

```
 ip rule add from 10.0.0.101 table 2000

 ip rule add from 10.0.0.102 table 2000
```

**5、永久配置策略路由和辅助IP**

rc.local实现，以ubuntu20.04为例，以下配置为同时使用辅助IP的配置步骤，未使用辅助IP时去掉辅助IP相关配置即可

1）、sudo vim /lib/systemd/system/rc-local.service

2）、文件后添加

```
[Install]  
WantedBy=multi-user.target  
Alias=rc-local.service
```

3）、创建rc.local

	sudo touch /etc/rc.local

4）、编辑rc.local

	 #!/bin/sh
	 ip route add default via 10.0.0.1 dev eth1 table 2000  #配置策略路由
	
	 ip rule add from 10.0.0.222 table 2000 #虚拟网卡主IP
	
	 ip addr add 10.0.0.101/24 dev eth1 #添加辅助IP，使用辅助IP时设置
	
	 ip addr add 10.0.0.102/24 dev eth1 #添加辅助IP，使用辅助IP时设置
	
	 ip rule add from 10.0.0.101 table 2000 #配置辅助IP策略路由，使用辅助IP时设置
	
	 ip rule add from 10.0.0.102 table 2000 #配置辅助IP策略路由，使用辅助IP时设置
	 exit 0

5）.修改权限

```
sudo chmod +x /etc/rc.local
```

6）.创建软连接

```
ln -s /lib/systemd/system/rc.local.service /etc/systemd/system/
```

7）.查看策略路由配置情况
 重启主机

**root@xx-xx-xx-xx:/home/ubuntu# ip route show table 2000** 

default via 10.0.0.1 dev eth1 

**root@xx-xx-xx-xx:/home/ubuntu# ip rule show**

0:   from all lookup local

32760: from 10.0.0.101 lookup 2000

32761: from 10.0.0.102 lookup 2000

32762: from 10.0.0.222 lookup 2000

32763: from all lookup main

32764: from all lookup default

添加的虚拟网卡主IP和辅助IP均可以ping通即配置完成。

#### 6、辅助IP绑定EIP后，配置策略路由步骤

新建策略路由表

```
echo '2001 ROUTER_IP_T' >> /etc/iproute2/rt_tables
```

配置策略匹配规则

```
ip rule add from X.X.X.X（辅助IP） table ROUTER_IP_T
ip rule add from X.X.X.X（辅助IP） table ROUTER_IP_T
```

配置策略路由

```
ip route add default via X.X.X.X（网关IP） dev eth1 table ROUTER_IP_T
```






## 常见问题FAQ

### 1、为什么配置的多网卡不通？

一般情况下是由于网卡的配置的原因，可按如下方式检查：

1、检查网卡是否已绑定云主机，且在云主机上配置了网卡

2、检查RPF是否已关闭

3、网卡路由是否正确

若检查过以上配置仍旧以上不通，可提供：

- 源IP到目的IP的五元组及每一跳信息
- 网卡与主机的绑定关系
- 资源所属子网信息
- 网卡的路由配置


交由[售后咨询](https://spt.ucloud.cn/)辅助查看。

### 2、云主机未开启网卡功能，想要开启第二张网卡，如何处理？

若云主机在创建时未开启网卡功能，后续无法开启，建议新建主机以使用网卡功能。
