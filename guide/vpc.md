# VPC



## 创建VPC

选择【产品与服务】中“私有网络VPC”，进入私有网络页面，在“VPC“标签页中点击“创建VPC”按钮进行创建。

![image](/images/guide/create_vpc1.png)

在弹出的页面中填写VPC名称，选择所需的网段，默认为192.168.0.0/16。然后点击“确定”。

![image](/images/guide/create_vpc2.png)

一个VPC内可允许多个独立地址段存在，只要互相没有地址重叠。

在创建VPC弹出的对话框中，选择“网段”下的“+”号，即可再添加一个网段。

![image](/images/guide/create_vpc3.png)

创建成功，可以在VPC标签页的VPC列表中查看所创建的VPC。

![image](/images/guide/create_vpc4.png)

## 添加网段

VPC创建后，可随时添加不重叠的多个网段。

在“VPC”标签页中点击“添加网段”按钮。

![image](/images/guide/add_address1.png)

在弹出的对话框中，填写要添加的网段信息。

![image](/images/guide/add_address2.png)

点击“确定”，成功添加。

## 查看VPC详情

在“VPC”标签页中点击“详情”按钮，进入某个VPC的详情页面。

![image](/images/guide/vpc_info1.png)

在详情页中，可以看到VPC的名称、资源id、备注、业务组、子网数、网段等基本信息。

![image](/images/guide/vpc_info2.png)

## 网络互通

两个VPC之间原本是互相隔离的，如因业务目的，需要两个VPC的网络能互相访问，可以通过VPC的“网络互通”功能实现。

在“VPC”标签页中点击“网络互通”按钮，进入“网络互通”操作页面，或在VPC详情页面中切换至“网络互通”标签。

![image](/images/guide/vpc_intercon1.png)

点击页面上的“联通VPC”按钮，进入“联通VPC”对话框。

![image](/images/guide/vpc_intercon3.png)

选择需联通的VPC，并确保VPC间的网段互相没有重合，点击“确定”。

![image](/images/guide/vpc_intercon4.png)

再次确认需联通的VPC信息，并点击“确定”。

![image](/images/guide/vpc_intercon5.png)

成功联通。若试图联通的VPC的地址段有重合，则网络互通失败并报错。

已联通的VPC会展示在“已联通的VPC”列表中。

![image](/images/guide/vpc_intercon6.png)

## 跨项目VPC互通

“网络互通”除打通同项目的不同VPC外，也允许不同项目间的VPC打通。

同上文一样，进入“网络互通”操作页面，并点击页面上的“联通VPC”按钮，进入“联通VPC”对话框。

![image](/images/guide/vpc_intercon3.png)

点击“所属项目”旁的下拉列表，选择目标项目。

![image](/images/guide/vpc_intercon7.png)

在目标项目的VPC列表中，选择要打通的VPC，并点击“确定”确认，完成打通。

## 跨地域VPC互通

“网络互通”除打通同地域的不同VPC外，也允许不同地域间的VPC打通。前提是：两个地域间已建立UDPN（高速通道）连接。

同上文一样，进入“网络互通”操作页面，并点击页面上的“联通VPC”按钮，进入“联通VPC”对话框。

![image](/images/guide/vpc_intercon3.png)

点击“所在地域”旁的下拉列表，选择目标地域。

![image](/images/guide/vpc_intercon8.png)

在目标地域的VPC列表中，选择要打通的VPC，并点击“确定”确认，完成打通。

**注：**

在高速通道UDPN产品中，也提供“网络互通”能力，功能和此处相同。

## 断开互通

已经联通的VPC，可以选择断开互通。

在“已联通的VPC”列表中，选择想断开连接的VPC。此时“断开VPC”按钮由不可用变为可用，点击按钮。

![image](/images/guide/vpc_intercon9.png)

在弹出的“断开VPC”对话框中，点击“确定”确认断开。

![image](/images/guide/vpc_intercon10.png)

成功断开。“已联通的VPC”列表的联通信息会相应更新。

## 删除VPC

在VPC列表中，选择需删除的VPC，并点击“删除”按钮。

![image](/images/guide/vpc_del1.png)

删除VPC前，确保VPC内的云资源和子网均已删除，并且不与其它VPC互通，否则会无法删除。

![image](/images/guide/vpc_del2.png)

在弹出的“删除VPC”对话框中，点击“确定”确认删除。

![image](/images/guide/vpc_del3.png)
