# 私有网络VPC

{{indexmenu_n>5}}

## 创建VPC

1.  登录控制台，选择产品列表下的VPC选项，点击"创建VPC"按钮。

![image](/images/create_vpc01.png)

1.  在弹出的对话框中，选择VPC的网段，目前支持 10.0.0.0/8、192.168.0.0/16和172.16-31.0.0/12
    三种地址段。

![image](/images/create_vpc02.png)

1.  点击"确定"，创建完成。 

## 删除VPC

1.  勾选要删除的VPC，并点击"删除"按钮。

![image](/images/delete_vpc01.png)

1.  在弹出的对话框中点击"确定"，完成删除。 

## 为VPC增加网段

只要和VPC原定义网段不存在冲突，就可以给VPC添加新的网段。

1.  勾选要删除的VPC，并点击"添加网段"按钮。

![image](/images/add_vpc_addr01.png)

1.  在弹出的对话框中，配置需要添加的网段，目前支持添加10、172、192全部三种网段。

![image](/images/add_vpc_addr02.png)

1.  点击"确定"，添加成功。 

## 在VPC中创建资源

下面以云主机UHost为例，说明如何在VPC中创建云主机。

当用户创建完VPC后，还需要创建子网。有了VPC和子网后，才能创建具体资源。

1.  在UHost产品页面，点击"创建主机"按钮。

![image](/images/create_uhost01.png)

1.  在弹出窗口中，依次选择地域可用区、主机配置、操作系统等。在"网络配置"部分，选择云主机所属的VPC和子网。

![image](/images/create_uhost02.png)

1.  点击"购买"按钮，完成创建。
