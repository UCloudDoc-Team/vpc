
# 内网VIP

内网虚拟IP（VIP），是UCloud云平台上专为云主机、物理云主机提供的，作为用户自定义高可用服务的可漂移内网入口。在使用VIP时，用户需要将VIP与高可用服务结合，以便在服务出现故障时进行服务入口漂移，实现服务的高可用。

## 申请内网VIP

选择菜单中"网络 UNet"主标签下的内网VIP选项进入产品主页面，点击"申请IP"按钮进行申请。

![image](/images/vip1.png)

在申请菜单中，可选择所申请的内网VIP所属的VPC和子网， 业务组，以及申请数量。

![image](/images/vip2.png)

**注意：**

申请完毕后，可在列表页中点击"编辑"功能，对内网VIP的名称进行修改，并可对内网VIP进行"更改业务组"操作。

## 将内网VIP应用于云主机/物理云主机上

**注意：一个VIP只能绑定在一台云主机/物理云主机上，否则可能会引发ARP信息的不一致。**

### CentOS系统实现方法一：

#### 固化配置，将内网虚拟IP写入ifcfg配置文件（针对CentOS7及其更低版本有效）

1、  复制一份ifcfg-eth0配置文件，命名为ifcfg-eth0:0，命令如下
```
    cp /etc/sysconfig/network-scripts/ifcfg-eth0 /etc/sysconfig/network-scripts/ifcfg-eth0:0
```

2、  修改ifcfg-eth0:0配置文件。命令如下

```
    vim /etc/sysconfig/network-scripts/ifcfg-eth0:0
```

进入编辑模式后，将"IPADDR="后面的IP改为您申请到的内网虚拟IP，将DEVICE="eth0"改为DEVICE="eth0:0"，删去"GATEWAY="这一行。


3、 重启网络。命令如下 :

```
    service network restart
```

4、  使用arping命令通告arp信息 :若您申请的内网虚拟IP为10.4.200.X

```
    arping -U 10.4.200.X
```

**注意：**

- 重启网络后，使用 ip a 命令检查内网虚拟IP是否添加成功。
- 内网虚拟IP仅可写入ifcfg-eth0:0配置文件，不可将ifcfg-eth0配置文件中的IPADDR修改为内网虚拟IP。如果错误修改了ifcfg-eth0配置文件，可能会导致云主机的网络中断。

#### 针对CentOS8及更高版本

CentOS8开始全面使用NetworkManager对网络进行管理，因此无法采用上述方式进行网络管理。

1、查看当前连接情况。

```
nmcli con show
```

可能的结果如下：
```
NAME         UUID                                  TYPE      DEVICE 
System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0 
```

获取当前的网络连接名字为：System eth0

2、增加IP

```
nmcli con mod "System eth0" +ipv4.addresses "192.168.0.30/24"
```

3、加载配置

```
nmcli con up "System eth0"
```

4、  使用arping命令通告arp信息 :若您申请的内网虚拟IP为10.4.200.X

```
    arping -U 192.168.0.30
```

### CentOS系统实现方法二：临时添加，使用命令手动添加内网虚拟IP

若您申请的内网虚拟IP为10.4.200.X

命令如下
```
    ip addr add 10.4.200.X/16 dev eth0
```
使用arping命令通告arp信息
```
    arping -U 10.4.200.X
```
使用 ip a 命令检查内网虚拟IP是否添加成功

**注意：**

主机重启或者网络服务重启后，需要重新手动添加内网虚拟IP

### Ubuntu系统实现方法一:固化配置，将内网虚拟IP写入ifcfg配置文件

修改网络配置文件，命令如下
```
    sudo vim /etc/network/interfaces
```
将配置文件段复制一份，eth0替换为eth0:0，并在address后填入申请到的内网虚拟IP
```
    # The primary network interface
    auto eth0
    iface eth0 inet static
    address 10.4.5.255
    netmask 255.255.0.0
    gateway 10.4.0.1
    dns-nameservers 10.255.255.1
```
Ubuntu修改后配置文件示例
```
    # The primary network interface
    auto eth0
    iface eth0 inet static
    address 10.4.5.255
    netmask 255.255.0.0
    gateway 10.4.0.1
    dns-nameservers 10.255.255.1
    auto eth0:0
    iface eth0:0 inet static
    address 此处填入申请的内网虚拟IP
    netmask 255.255.0.0
    gateway 10.4.0.1
    dns-nameservers 10.255.255.1
```
重启网络，命令如下 :
```
    sudo /etc/init.d/networking restart
```
使用arping命令通告arp信息 :
```
    arping -U 10.4.200.X
```
使用 ip a 命令检查内网虚拟IP是否添加成功

**特别提醒：**


内网虚拟IP仅可添加在eth0:0配置段内，不可修改eth0配置段中的address，如果错误修改了eth0配置段，可能会导致云主机的网络中断。

### Ubuntu系统实现方法二：临时添加，使用命令手动添加内网虚拟IP

若申请的内网虚拟IP为10.4.200.X，命令如下
```
    ip addr add 10.4.200.X/16 dev eth0
```
使用arping命令通告arp信息 :
```
    arping -U 10.4.200.X
```
使用 ip a 命令检查内网虚拟IP是否添加成功。

**特别提醒：**

主机重启或者网络服务重启后，需要重新手动添加内网虚拟IP。

## 解除资源中所配置内网虚拟IP

### CentOS系统将内网虚拟IP写入ifcfg-eth0:0配置文件的情况

删除ifcfg-eth0:0配置文件，命令如下
```
    rm /etc/sysconfig/network-scripts/ifcfg-eth0:0
```
重启网络，命令如下
```
    service network restart
```
使用 ip a 命令检查内网虚拟IP是否删除成功。

CentOS系统使用命令手动添加内网虚拟IP的情况，若申请的内网虚拟IP为10.4.200.X，命令如下：
```
    ip addr del 10.4.200.X/16 dev eth0
```
使用 ip a 命令检查内网虚拟IP是否删除成功。

### Ubuntu系统将内网虚拟IP写入配置文件的情况

修改网络配置文件，命令如下
```
    sudo vim /etc/network/interfaces
```
将配置文件段中auto eth0:0配置段删除。

Ubuntu配置文件中需要删除的配置段示例：
```
    auto eth0:0
    iface eth0:0 inet static
    address 此处应为您申请的内网虚拟IP
    netmask 255.255.0.0
    gateway 10.4.0.1
    dns-nameservers 10.255.255.1
```
重启网络，命令如下：
```
    sudo /etc/init.d/networking restart
```
使用 ip a 命令检查内网虚拟IP是否删除成功。

### Ubuntu系统使用命令手动添加内网虚拟IP的情况

若申请的内网虚拟IP为10.4.200.X，命令如下：
```
    ip addr del 10.4.200.X/16 dev eth0
```
使用 ip a 命令检查内网虚拟IP是否删除成功。

## 释放VIP

1.  在内网VIP的功能页面勾选需要释放的内网虚拟IP，点击功能按钮中的"释放"。

![image](/images/vip3.png)

2.  在弹出的释放虚拟IP对话框中，展示了内网虚拟IP的相关信息，确认无误后点击"确定"就可以完成内网虚拟IP的释放。

## 使用VIP与Keepalive配置服务高可用

CentOS 6.x 和 Ubuntu 12+系统，参见
[使用VIP与Keepalive配置服务高可用](/uhost/public/keepalived)
