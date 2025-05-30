## 构建IPV6双栈网络

1. 创建双栈VPC

支持在创建虚拟私有网络VPC时，开启IPv6功能，由系统会自动分配IPv6网段

![img](/images/391e81fa-7738-4f20-98af-d559280999ae.png)

支持存量私有网络VPC开启IPV6网络

![img](/images/cf80b4c7-7a64-4cc0-a03f-bc0b80c439bd.png)

2. 创建双栈子网

支持在创建子网时，开启IPv6功能，由系统会自动分配IPv6网段

![img](/images/bd20d62a-a051-432d-b23f-e54addacab04.png)

支持存量子网开启IPV6网络

![img](/images/249526ad-8bfa-42cc-a81f-931b6aebd356.png)

3. 创建双栈资源

以云主机为例，创建双栈云主机时，需要选择开启了IPV6功能的VPC和子网，且镜像为centos7.9 EOL 或Debian11.7

![img](/images/814cf098-1a3c-44b7-b913-2a33aaa81397.png)

存量云主机申请IPv6地址

![img](/images/e80bf293-7812-401b-9a7d-21d9b72fb89a.png)

4. 创建IPV6网关，开通IPv6公网

暂不支持直接对整个IPv6网段开通公网带宽，仅支持单个IPv6地址开通公网带宽。

1） 登录VPC控制台，选择IPv6网关的地域。

2） **选择IPv6网关页签**，单击创建IPv6网关。

![img](/images/78255411-f3e6-47b4-9e44-ca976c3b9653.png)

3） 在IPv6网关的详情页面，找到目标IPv6地址，然后在**操作**列单击**开启公网带宽**。

4） 在**IPv6公网带宽**页面，选择计费方式和设置带宽，然后单击**立即购买**。

![img](/images/18e04a89-d554-4ab2-a613-e6084bf302f2.png)



