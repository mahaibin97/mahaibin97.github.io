交换（switching）基础
===================

> 在学习交换协议之前，我们必须掌握**以下知识**：
> 
> 交换相关设备及数据在2层的传递
> 数据帧的类型
> VLAN
> Trunk-link
> VTP

## 交换相关设备

###### 集线器（[HUB](https://en.wikipedia.org/wiki/Hub_(network_science))）
HUB是一个多端口的转发器，在以HUB为中心设备时，即使网络中某条线路产生了故障，并不影响其它线路的工作。
所以HUB在局域网中得到了广泛的应用。大多数的时候它用在星型与树型网络拓扑结构中，以RJ-45接口与各主机相连（也有BNC接口），HUB按照不同的说法有很多种类。 HUB按照对输入信号的处理方式上，可以分为无源HUB、有源HUB、智能HUB。

> HUB工作在物理层，对网络消息起到放大、扩散的作用。

**集线器的缺陷**

-  泛洪数据不安全；
-  数据转发机制容易遭遇冲突域；
-  数据转发机制易造成极大的广播域。

###### 交换机（switch）
交换机位于接入层，负责终端用户接入网络，同时，交换机可以避免冲突域以及划分广播域。
常用的交换机是以太网交换机，还有一些特殊用途的交换机，例如电话语音交换机、FC光纤交换机等。

> 交换机一般工作在2层，也就是MAC层。但还有一类交换机，叫多层交换机，工作在2层和3层之间，既具备交换功能，又有一定的路由功能。

> 交换机转发数据机制
> 1. 数据帧格式 **帧头/IP包头/端头（端口号）/data/帧尾**
> 2. 交换机读取帧头目的MAC进行数据转发；
> 3. MAC列表其实就是交换机转发数据的依据，交换机读取帧目的MAC来和MAC地址列表中中的条目进行对比，并且找到可用路径，进行数据的转发。

**交换机MAC地址列表的构建方式**

1. 交换机自动学习目标MAC，构建MAC地址列表；
2. 管理员手动添加MAC地址条目
	交换机将接口的数据的源MAC和接受接口关联，产生MAC地址条目用于转发数据。

## 数据帧的类型和网络消息的类型

1. 单播帧 [unicast flood](https://en.wikipedia.org/wiki/Unicast_flood)
数据来自**一对一的通信**;
已知MAC的单播帧：交换机拥有抵达目的MAC的路径，沿路径定向转发；
对于未知目的MAC的单播帧：交换机没有抵达目的MAC的路径，直接进行泛洪。

2. 广播帧 [brodcast](https://en.wikipedia.org/wiki/Broadcasting_(networking))
数据来自**一对所有**
处理方式：直接泛洪；

3. 多播帧 [multicast](https://en.wikipedia.org/wiki/Multicast)
**数据来自一对多**
泛洪处理

###### 交换机处理冲突域和广播域的方式

1. 交换机处理冲突域的方法
交换机每一个接口是一个冲突域

2. 交换机处理广播域的方法
交换机对广播流和多播流都是泛洪
整个交换机就是一个大的广播域，而若干个交换机相连会扩大广播域，交换机支持VLAN来分割广播域。

## VLAN（virtual local access network）
> VLAN 虚拟的本地接入网络
> 在交换机上创建VLAN，把指定的接口划入vlan，从而达到**划分广播域**的目的。
> 一个网段为一个部门，一个部门对应到一个vlan，不同vlan间的广播流是不能跨越的。

###### 详解
1. VLAN的详解
交换机默认创建vlan 1，默认所有交换机端口划入vlan 1.

2. vlan的命名
编号命名、字符型命名

3. vlan编号的含义

> Vlan 1，默认存在，本帧vlan；
Vlan 2~1001，常规vlan，是用于管理员创建vlan时所用；
Vlan1002~1005，保留vlan；
Vlan1006~4096，扩展vlan，交换机暂不支持。

4. vlan的通讯机制
同一网段下，同一vlan下不同接口广播流可以传递且可以通信；
同一网段下，不同vlan下端口间广播流不可以传递也不可以通信；
不同交换机上相同vlan的接口无法通信，因为不同交换机间vlan流量传递时**未带任何标记**，所以不同vlan流量无法定向传递，没有携带vlan标记的数据都进**vlan 1**.

###### 配置	
```
switch1#vlan database  					 			 //进入VLAN配置模式
		vlan <VLAN-ID> name <VLAN-name>  			 //创建VLAN，创建多个只需重复此命令

switch#show vlan			 						 //查看VLAN情况
switch(config)#interface <接口>   		 			 //进入接口
或者，也可以一次性进入多个接口
switch(config)#interface range <接口>    			 //接口可以是fa0/0-1,fa1/0等；
switch(config-if)#switchport mode access 			 //修改接口模式
switch(config-if)#switchport access vlan <VLAN-ID>   //将接口划入vlan 
switch#show vlan                                     //校验VLAN情况
```

## Trunk—link 干道链路

Trunk-link是一种逻辑链路，用于实现一些特定功能。

交换机间链路启用Trunk-link，那么不同vlan的流量就可以携带vlan标记进行传递。
不同交换机相同vlan通讯，必须依赖于Trunk-link。

###### 详解

1. Trunk-link携带vlan标记的方法

-  IEEE802.1Q　vlan标记携带标准
将vlan标记插入帧头中，目的MAC和源ＭＡＣ的后面。

-  cisco开发ISL携带vlan标记标准
**内部交换标签**

> ISL头/帧头/包头/段头/数据/帧尾/ISL尾


###### 配置

```
switch(config)#interface <接口>   
switch(config)#switchport trunk encapsulation dot1q/isl   //封装，2层思科设备默认只支持802.1Q，不支持此命令
switch(config)#switchport mode trunk                      //接口模式
switch(config)#exit										  //退出配置模式
switch#show interface brief								  //校验trunk配置

!!补充一条命令：
修改Trunk-link承载列表
Interface trunk allowed valn <1,3-1005>					  //如上命令只允许valn1和vlan3-1005通过，而vlan2不允许通过。
```


## VTP（VLAN trunk protocol） 虚拟链路协议
VTP是一种用来同步VLAN配置的机制。
该机制通过一套同一的域名和密码，将server机和client机相互配对后，把server交换机上的vlan同步到client交换机上去。

**但需要注意的是**：
1. trunk必须提前配好；
2. 只建立VLAN，不划分接口；
3. 实际工作中，由于难度等问题，一般不使用。

###### VTP的工作机制

> VTP默认开启

-  域名
Domain 默认为空
-  域的口令（password）
-  VTP的模式（mode）
--  sever服务器模式（默认模式）
--  client客户模式
--  transparent 透明模式
--  off 关闭模式（此模式下VLAN自行配置）

> 补充
- sever，拥有VLAN的创建、修改与删除权限，同时还拥有VLAN的保存权限，sever能够发送并接受VTP消息；
- client，不具备VLAN的创建、修改、删除与结果保存权限，只能接受与传递ＶＴＰ同步消息；
- 透明模式，不被同步但透传，创建、修改VLAN.

-  修定号
修定号默认为0，随着VLAN的配置改变而自加1.
**（如果两个sever相遇，修定号大的说了算）**


###### 配置

1. 2层交换机VTP的配置
```
switch(config)#vtp mode <server/client/transparent/off>  
				vtp domain <域名>
				vtp password <密码>
switch#show vtp status              //校验VTP
	   do show vtp password
	   do show vtp status 
	   vtp v2
```
**注意**

> 每个client都要与sever域名、密码相同。

2. 3层交换机VTP配置
```
switch(config)#vtp mode <server/client/transparent/off>  
				vtp domain <域名>
				vtp password <密码>
switch#show vtp status              //校验VTP
	   do show vtp password
	   do show vtp status 
	   vtp v3
```	




[参考文献]
1. Wikipedia/[VLAN](https://en.wikipedia.org/wiki/Virtual_LAN)
2. RFC [3069](https://www.rfc-editor.org/rfc/rfc3069.txt)	
3. InfoQ [Switching](http://www.it-infor.net/china-ccie/1%20CCIE%E8%B5%84%E6%96%99%E5%BA%93/CCIE%20R&S%20v5.0%20%E7%90%86%E8%AE%BA/Switching.pdf)