# 虚拟网卡

本文档仅介绍虚拟网卡、辅助IP等与产品页相关功能操作。

## 名词解释

* 默认网卡：主机开启网卡功能时由系统默认创建的网卡，原本绑定在云主机的EIP和防火墙都将落在默认网卡上。

* 自定义网卡：控制台手动创建的网卡，可与云主机弹性绑定。

* 辅助IP：控制台手动申请辅助IP，与主IP配合使用，可与云主机弹性绑定，可申请数量与云主机配置有关。

未开启网络增强特性

| **序号** | **vCPU**        | **弹性网卡数量** | **单网卡私有IP数量** |
| -------- | --------------- | ---------------- | -------------------- |
| 1        | 1 ≤ vCPU ≤ 2    | 2                | 2                    |
| 2        | 2 < vCPU ≤ 4    | 3                | 3                    |
| 3        | 4 < vCPU ≤ 8    | 4                | 4                    |
| 4        | 8 < vCPU ≤ 32   | 8                | 6                    |
| 5        | 32 < vCPU ≤ 64  | 12               | 8                    |
| 6        | 64 < vCPU ≤ 128 | 15               | 10                   |
| 7        | VCPU > 128      | 15               | 12                   |

网络增强2.0特性下:

| **序号** | **vCPU**        | **弹性网卡数量** | **单网卡私有IP数量** |
| -------- | --------------- | ---------------- | -------------------- |
| 1        | 1 ≤ vCPU ≤ 2    | 1                | 4                    |
| 2        | 2 < vCPU ≤ 4    | 1                | 9                    |
| 3        | 4 < vCPU ≤ 8    | 1                | 16                    |
| 4        | 8 < vCPU ≤ 32   | 1                | 48                    |
| 5        | 32 < vCPU ≤ 64  | 1                | 96                    |
| 6        | 64 < vCPU ≤ 128 | 1                | 150                   |
| 7        | VCPU > 128      | 1                | 180                   |


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

创建虚拟网卡云主机后，云主机默认网卡的主无需配置，对应的辅助IP需要进行配置，但目前部分地域的镜像支持网卡的辅助IP无需配置，控制台绑定后即可自动完成配置。

### 网卡IP自动化配置

部分镜像预装了自动配置工具，可以自动配置网卡的辅助IP。如果您使用这类镜像，则无需自行配置网卡的辅助IP，该功能目前在灰度中，如有需求请联系技术支持。

操作之前，请先确认是否满足如下信息；

#### 使用网卡IP自动化配置

- 创建开启虚拟网卡功能的云主机
- 云主机系统版本为Ubuntu 20.04
- 开启了自动化配置功能
- 控制台添加或删除IP后，可以通过重启云主机或者执行下发命令行的方式，让修改的IP信息生效

```
sudo cloud-init init
```

**注意事项**

- 开启EIP网卡可见模式后，EIP和内网IP均支持自动化配置。
- 控制台操作完成后，配置下发预计10-30s左右，

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
(辅助IP) 10.40.44.17
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

#### 第二步：配置自定义网卡eth1

eth1的网卡写配置文件，此处配置主IP即可

```bash
# cp -f /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth1
# vim /etc/sysconfig/network-scripts/ifcfg-eth1
修改文件中的

- DEVICE=虚拟网卡的网卡名
- HWADDR=虚拟网卡的MAC地址
- IPADDR=虚拟网卡的主IP地址

示例：
DEVICE=eth1
HWADDR=52:54:00:1B:5E:57
IPADDR=10.40.54.131
```

通过nmc配置eth1主网卡的策略路由；

新建策略路由表

```bash
# nmcli c modify System\ eth1 +ipv4.route-table 101
```

配置策略路由

```bash
# ip rule
0:	from all lookup local
32766:	from all lookup main
32767:	from all lookup defaul
//未配置过任何策略路由，priority从32765开始递减
# nmcli c modify System\ eth1 +ipv4.routing-rules "priority 32765 from 10.40.54.131 table 101"
# nmcli c show System\ eth1 | grep -E 'ipv4.route-table|ipv4.routing-rules'
ipv4.route-table:                       101
ipv4.routing-rules:                     priority 32765 from 10.40.54.131 table 101
```

重启网络服务，并检查策略路由规则

```bash
#nmcli c reload
#nmcli c up System\ eth1
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/...)
#ip rule
...
32765:   from 10.40.54.131 lookup 101
...
```

添加的虚拟网卡主IP，若有绑定外网弹性ip防火墙规则放行后可以ping通，即配置完成


#### 第三步：配置eth1的辅助ip

配置前请确认辅助IP子网掩码，和主IP保持一致即可；

将IP地址替换成待绑定辅助IP地址，配置默认网卡辅助IP只需将网卡名称改成所绑定的ethx，以此类推

```bash
# nmcli c modify System\ eth1 +ipv4.addresses X.X.X.X（辅助IP）/子网掩码
# nmcli c show System\ eth1 | grep ipv4.addresses
ipv4.addresses:                         10.40.54.131/16, $X.X.X.X（辅助IP）/子网掩码

```

通过nmcli配置eth1辅助ip策略路由

```bash
# nmcli c modify System\ eth1 +ipv4.routing-rules "priority 32764 from X.X.X.X（辅助IP） table 101"
# nmcli c show System\ eth1 | grep ipv4.routing-rules
ipv4.routing-rules:                     priority 32765 from 10.40.54.131 table 101, priority 32764 from X.X.X.X（辅助IP） table 101
```

重启网络服务，并检查策略路由规则

```bash
# nmcli c reload
# nmcli c up System\ eth1

#ip rule
...
32764:   from X.X.X.X（辅助IP） lookup 101
32765:   from 10.40.54.131 lookup 101
...
```

添加的虚拟网卡主IP和辅助IP均可以ping通即配置完成




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

## EIP网卡可见模式

EIP以**EIP网卡可见模式**与虚拟网卡绑定，您可以在操作系统的网卡信息中查看EIP信息，以centos7.X系统为例。

操作之前，请先确认是否满足如下信息；

### 开启EIP网卡可见

- 您已经创建了开启虚拟网卡功能的云主机。
- 您已经在控制台开启了EIP网卡可见功能。

**注意事项**

- 开启EIP网卡可见模式后，需在云主机配进行单独的配置，才能正常使用该能力。
- 一个网卡开启EIP网卡可见功能后，可以同时绑定内网IP和外网IP，内网IP如果想访问公网，需通过绑定nat网关形式才能具备功能的能力。

**关闭RPF**

编辑/etc/sysctl.conf文件， 修改net.ipv4.conf.all.rp_filter值为0，然后重启服务器

```Shell
vi /etc/sysctl.conf
net.ipv4.conf.all.rp_filter = 0 
```

**配置自定义网卡**

```Shell
ifconfig eth1 EIP地址 netmask 255.255.0.0
ifconfig eth1 mtu 1454
echo "101 net_101 " >> /etc/iproute2/rt_tables
ip r 查询网关地址
ip r add default via 网关地址 dev eth1 src EIP地址 onlink table 101
ip rule add from EIP地址 table net_101
```

**创建配置文件**

```Shell
复制文件
cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth1
修改文件内容
 vi /etc/sysconfig/network-scripts/ifcfg-eth1
修改文件中的
- DEVICE=虚拟网卡的网卡名
- HWADDR=虚拟网卡的MAC地址
- IPADDR=虚拟网卡的IP地址
```

**策略路由写配置文件**

```Shell
vi /etc/sysconfig/network-scripts/route-eth1

default via 网关地址 dev eth1 src EIP地址 onlink table net_101

vi /etc/sysconfig/network-scripts/rule-eth1

from EIP地址 table net_101
```

**设置默认出口**

开启EIP网卡可见后，云主机的默认出口为主网卡的内网IP，如向互联网发送请求时，未指定源IP，此时会出现网络不通的问题。

方式一，修改路由文件，指定源IP

```Shell
ip route add default via 10.50.0.1 dev eth0 src 【默认出外网的EIP】
```

方式二，指定默认出口，在虚拟网卡详情页，选择指定IP座位默认出口后，重启云主机或运行如下命令即可完成，（目前仅支持主机镜像为 Ubuntu 20.04 的虚拟网卡可以通过该方式设置）

```Shell
sudo cloud-init init
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

### 3、EIP网卡可见模式下，为什么访问同网段的IP不通？

若您开启了EIP网卡可见模式，在配置网卡文件时，必须配置为/32的地址，否则会导致同网段的IP访问出现问题。

