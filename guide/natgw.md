# NAT网关



NAT网关是一款企业级的VPC公网网关，可以让子网中未绑定弹性IP的云资源访问外网，也可配置端口转发规则使云资源对外提供服务。

## 创建NAT网关

-  登录控制台，选择【产品与服务】中“私有网络VPC”，进入私有网络页面，选择NAT网关标签页

![image](/images/natgw_guide.png)

-  点击‘创建NAT网关’，弹出新建窗口。

![image](/images/natgw_create.png)

-  新建窗口中，选择VPC和子网，出外网模式选择‘普通模式’，选择NAT网关所需的外网IP、带宽和防火墙，随后点击‘确定’。



-  创建完成，在列表中可以见到NAT网关的信息。

![image](/images/natgw_list.png)

-  NAT网关创建完后，指定子网中未绑定弹性IP的云资源，即可通过NAT网关出外网。

## 白名单模式

NAT网关另外提供白名单模式。

白名单模式下，在NAT网关指定子网中、并且在白名单中定义的云资源，才可通过该NAT网关出外网。

-  创建NAT网关，除选择‘白名单模式’外，其余和‘普通模式’相同。



-  刚建好的NAT网关，白名单为空，没有云资源能访问外网，需要配置白名单。在列表中点击‘管理’按钮，进入NAT网关详情页。

![image](/images/natgw_detail.png)

-  点击‘模式管理’标签，进入白名单管理。

![image](/images/natgw_mode.png)

-  点击‘添加白名单’，添加想要的主资源。

![image](/images/natgw_add_list.png)

-  点‘确定’后，名单生效，在白名单中的云资源可以出外网。 

## 端口转发

配置端口转发，可以让云资源的某个端口被外网访问，以便提供服务或进行管理。

-  在NAT网关详情页，切换到‘端口转发’标签。

![image](/images/natagw_rule_manage.png)

-  点击‘添加转发规则’，可新增规则。

![image](/images/natgw_add_rule1.png)

-  点击‘修改’按钮，可以修改已有规则。



-  点击‘删除’按钮，可以删除已有规则。

![image](/images/natgw_rule_detail.png)

## 配置网络出口

配置网络出口，可为NAT网关指定子网中的单个云资源指定弹性IP访问外网，也可为所有云资源指定同一弹性IP访问外网。

在NAT网关详情页，切换到‘外网弹性IP’标签，在“外网弹性IP”模块中，可对NAT网关绑定的弹性IP进行管理，

![image](/images/natgw_eip_bind.png)

-  点击“绑定”按钮，可选择将未绑定状态的弹性IP绑定至该NAT网关。

![image](/images/natgw_eip_bind01.png)

-  点击“解绑”按钮，可将目前绑定在NAT网关的弹性IP解绑。

![image](/images/natgw_eip_bind02.png)

-  点击“更改带宽”按钮，可调整当前弹性IP的带宽上限。

![image](/images/natgw_eip_bind03.png)

在“出口规则”模块中，可管理NAT网关的出口，
![image](/images/guide/natgw_outbound_rule01.png)

-  默认存在一条“默认出口规则”，用于记录该NAT网关的默认出口。可通过“编辑”按钮进行修改，但无法删除。默认出口规则优先级最低。

![image](/images/guide/natgw_default_rule.png)

-  点击“编辑”按钮，可添加出口规则，可为单个云资源指定出口。

![image](/images/guide/natgw_outbound_rule_add.png)

-  添加成功后，可修改规则。其中默认规则可修改目标IP，即出口IP。其他出口规则可修改名称和目标IP

![image](/images/guide/natgw_outbound_update.png)

## 混合云（托管云）使用NAT

第一步：托管机器默认路由指给内网CE。

第二步：物理网络添加指向内网网关的路由。

第三步：用户在NAT页面创建所属VPC为“托管云VPC”的NAT实例。

> 目前仅支持北京二、上海二、广州、香港、新加坡
