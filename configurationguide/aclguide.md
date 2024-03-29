# ACL规划

当前新建的ACL表默认是黑名单模式的，默认的出站和入站规则均为优先级最低的“全部放行”规则。在实际场景中，ACL由于其无状态的性质，设定非常复杂。下文将介绍ACL规则设定的要点，以及如何根据场景设置恰当的ACL规则。

## ACL规则建议

设定ACL规则的时候，建议如下：
  * ACL规则是无状态的，因此设定规则的时候需要同时考虑出站、入站两个方向。
  * UCloud ACL产品默认生效级别为单一云资源。例如添加一条目标地址为0.0.0.0/0的拒绝规则，那么主机与同子网内的主机的互通也会受到影响。因此需要额外添加一条针对同子网网段的放行规则。
  * 同一张ACL表中的不同入站规则，不允许优先级相同。同一张ACL表中的不同出站规则，不允许优先级相同。
  * ACL规则设置，需要尽可能的靠近流量的源头。例如禁止某个IP对子网资源的访问，在出站规则和入站规则做黑名单都可以达到效果。应当设置入站规则，拒绝流量流入。
  * UCloud公共服务网段默认放行。

## ACL案例

下面通过一个例子来介绍如何配置ACL规则。

网络架构如下图：

![](/images/configurationguide/未命名文件_5_.png)

在这个例子中，需要为UCloud广州Region的子网A配置ACL规则。子网A需要满足如下规则：
  * 子网A的网段为10.10.1.0/24,子网内部全部互通。
  * 子网A的22端口，能且仅能被子网B访问，子网B的网段为192.168.1.0/24.
  * 子网A的云资源仅能访问8.8.8.8的53端口（UDP/TCP），不能访问其他外网地址。
  * 子网A的云资源 80端口能被任意地址访问。
  * 子网A可以正常使用UCloud提供的公共服务。

其余流量均禁止。

那么子网A的ACL规则应当配置如下：

  * 入站规则

| 优先级   | 目标端口        | 协议  | 源地址            | 策略 | 描述                                |
| ----- | ----------- | --- | -------------- | -- | --------------------------------- |
| 1     | 22          | TCP | 192.168.1.0/24 | 接受 | 允许子网B访问22端口                       |
| 2     | 80          | TCP | 0.0.0.0/0      | 接受 | 允许任意地址访问80端口                      |
| 3     | 32768-65535 | TCP | 8.8.8.8/32     | 接受 | 允许子网内主机访问8.8.8.8的TCP 53端口，临时端口放行。 |
| 4     | 32768-65535 | UDP | 8.8.8.8/32     | 接受 | 允许子网内主机访问8.8.8.8的UDP 53端口，临时端口放行。 |
| 5     | ALL         | ALL | 10.10.1.0/24   | 接受 | 允许子网内主机互通                         |
| 6     | ALL         | ALL | 10.13.192.0/18 | 接受 | 允许公共服务的访问                         |
| 30000 | ALL         | ALL | 0.0.0.0/0      | 拒绝 | 默认拒绝所有流量                          |
| \*    | ALL         | ALL | 0.0.0.0/0      | 接受 | 默认放行所有流量，创建时系统自动添加。优先级最低。         |

  * 出站规则

| 优先级   | 目标端口        | 协议  | 目标地址           | 策略 | 描述                              |
| ----- | ----------- | --- | -------------- | -- | ------------------------------- |
| 1     | 53          | TCP | 8.8.8.8/32     | 接受 | 允许子网内主机访问8.8.8.8的TCP53端口        |
| 2     | 53          | UDP | 8.8.8.8/32     | 接受 | 允许子网内主机访问8.8.8.8的UDP53端口        |
| 3     | 32768-65535 | TCP | 0.0.0.0/0      | 接受 | 允许80端口对外访问，允许22端口对子网B访问，临时端口放行。 |
| 4     | ALL         | ALL | 10.10.1.0/24   | 接受 | 允许子网内主机互通                       |
| 5     | ALL         | ALL | 10.13.192.0/18 | 接受 | 允许公共服务的访问                       |
| 30000 | ALL         | ALL | ALL            | 拒绝 | 默认拒绝所有流量                        |
| \*    | ALL         | ALL | 0.0.0.0/0      | 接受 | 默认放行所有流量，创建时系统自动添加。优先级最低。       |

 
## ACL规则分析

以“子网A的22端口，能且仅能被子网B访问，子网B的网段为192.168.1.0/24.”这条规则为例，分析方式如下：
![](/images/configurationguide/sdngw_ecmp_6_.png)

其中临时端口是TCP、UDP等协议在主动发起连接的时候，从预设的范围内可以分配到的端口。该端口仅在连接生命周期内处于被占用状态。该范围可以通过以下方式获得：

    cat /proc/sys/net/ipv4/ip_local_port_range

可以利用下列命令进行临时端口范围的修改：

    echo "32768 65535" >  /proc/sys/net/ipv4/ip_local_port_range

本文使用“32768-65535”指代临时端口。
如上，分别标记出子网A的22端口被子网B访问的四元组。那么在默认规则为拒绝的条件下，需要添加如下入站和出站规则：


  * 入站规则

| 优先级 | 目标端口 | 协议  | 源地址            | 行为 | 描述          |
| --- | ---- | --- | -------------- | -- | ----------- |
| 1   | 22   | TCP | 192.168.1.0/24 | 接受 | 允许子网B访问22端口 |


  * 出站规则

| 优先级 | 目标端口        | 协议  | 目标地址           | 行为 | 描述          |
| --- | ----------- | --- | -------------- | -- | ----------- |
| 1   | 32768-65535 | TCP | 192.168.1.0/24 | 接受 | 允许子网B访问22端口 |

其余场景，也可以通过列举入向、出向的五元组（源目的IP、端口，以及使用协议）分析得到。
