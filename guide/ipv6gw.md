## 构建IPV6双栈网络

1. 创建双栈VPC

支持在创建虚拟私有网络VPC时，开启IPv6功能，由系统会自动分配IPv6网段

![img](/images/391e81fa-7738-4f20-98af-d559280999ae.png)

支持存量私有网络VPC开启IPV6网络

![img](/images/cf80b4c7-7a64-4cc0-a03f-bc0b80c439bd.png)

1. 创建双栈子网

支持在创建子网时，开启IPv6功能，由系统会自动分配IPv6网段

![img](/images/bd20d62a-a051-432d-b23f-e54addacab04.png)

支持存量子网开启IPV6网络

![img](/images/249526ad-8bfa-42cc-a81f-931b6aebd356.png)

1. 创建双栈资源

以云主机为例，创建双栈云主机时，需要选择开启了IPV6功能的VPC和子网，且选择镜像为centos7.9 EOL 或Debian11.7才支持分配IPV6 IP

![img](/images/814cf098-1a3c-44b7-b913-2a33aaa81397.png)

存量云主机申请IPv6地址

![img](/images/e80bf293-7812-401b-9a7d-21d9b72fb89a.png)