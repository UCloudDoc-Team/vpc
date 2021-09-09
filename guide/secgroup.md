# 安全组

## 创建支持安全组的云主机

1、创建新的VPC     [创建VPC](https://console.ucloud.cn/vpc/create)

2、点击创建主机

**（1）地域**：选择【华北（北京）可用区C】

**（2）主机配置**：点击【自定义配置】，CPU平台选择【Intel】-【快杰型0-IceLake及以上】（仅部分机型支持）

![image](/images/guide/secgroup4.png)

**（3）网络配置**：点击【自定义网络】，所属VPC选择刚刚**新建的VPC**。

![image](/images/guide/secgroup5.png)

说明：
- 上述指南未说明的配置，可按需选择，不对安全组的生效产生影响。
- 当前安全组不支持虚拟网卡，故需使用安全组的机器不可开启网卡功能。

## 创建安全组

登录控制台，【全部产品】中选择[安全组](https://console.ucloud.cn/vpc/secgroup)，点击【创建】。

![image](/images/guide/secgroup1.png)

说明：
- 新建安全组时，系统会自动添加常见规则，可按需选择保留或者删除。

## 绑定资源

在列表页点击【安全组名称】或【详情】，即可打开安全组详情页。在概览页，可对安全组应用的资源进行管理。

![image](/images/guide/secgroup2.png)

说明：
- 仅部分云主机支持绑定安全组。若需存量云主机支持安全组，需联系客户经理或者售后资源对资源进行迁移。


## 编辑规则

点击详情页【入站规则】或【出站规则】，即可对出入站规则进行管理。在添加或编辑规则时，可根据控制台使用说明录入所需的规则。

![image](/images/guide/secgroup3.png)

说明：
- 安全组规则的说明，详见[安全组产品说明](https://docs.ucloud.cn/vpc/introduction/secgroup?id=%e4%ba%a7%e5%93%81%e8%af%b4%e6%98%8e)。
