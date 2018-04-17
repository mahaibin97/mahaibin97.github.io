MSTP(multi spanning tree protocol) 多协议生成树
============

### MSTP的机制

MSTP允许多个VLAN映射到一个无环拓扑中（这样的无环拓扑也叫实例，instance）。解决了STP和RSTP64棵生成树上限的问题。
MSTP实际上利用STP和RSTP去为巨型交换网络创建无环拓扑。

**MSTP解决了两个核心问题**

1. 交换机创建的PVST无环拓扑上限问题--->MSTP创建之后，只能有16个实例（0-15），0为默认实例，所有VLAN默认使用，instance 0 来维护无环拓扑；

2. MSTP提出了层次化的生成树拓扑方案

> SW 1 ------------ SW 2
> VLAN 1--100   分为2个实例：偶数和奇数

### MSTP中的IST和CST概念

1. CST 通用生成树，CST是利用STP计算维护的无环拓扑
CST参与计算的设备为汇聚层和核心层；

2. IST 内部生成树，IST是利用RSTP计算维护的无环拓扑，
IST参与计算设备是部门内汇聚层与接入层；

3. MSTP如何区别IST和CST
MSTP的域名和修定号
每个IST都应该用部门的名字当做域名，修定号统一即可。

### MSTP的配置

1. 步骤1：将参与MSTP的交换机模式从STP/RSTP修改成MSTP
```
switch(config)#spanning-tree mode mst
```

2. 设置MSTP的参数

```
switch(config)#spanning-tree mst configuration
switch(config-mst)#name <域名>
				   revation <1>  	//一个部门一个值
				   instance <1-15> vlan 10,20,30  ////将某些vlan接口划入特定实例
校验命令：
switch#show spanning-tree mas <x>  //查看特定MSTP的实例无环拓扑结构

switch#show spanning-tree mst configuration   //查看MSTP的配置信息

```

3. MSTP中修改根桥、根端口

```
spanning-tree mst <instance-ID> root <primary/secondary>
spanning-tree mst <instance-ID> cost <xx>
spanning-tree mst <instance-ID> port-priority <xx>
```

### 补充知识

1. 网关，gateway
网关是一个设备，可以提供跨网段的访问；
路由器或者是多层交换机都可以称为网关；

2. 网关IP
网关IP是网关的标识，计算机利用它找到网关，再利用网关跨网段通讯；


### MSTP实验

##### 1. 实验需求

-  给MLSW1与MLSW2创建VLAN1---10；
-  在MLSW1,2上启用MSTP；
-  MLSW1，2分别创建两个实例，将奇数VLAN与偶数VLAN划入不同实例（奇数，实例1；偶数，实例2）；
-  将MLSW1设置为实例1的根桥并作为实例2的备份；
-  将MLSW2设置为实例2的根桥并作为实例1的备份。
-  奇数VLAN实例流量走E0/0 ,偶数VLAN实例流量走E0/1

##### 2. 实验拓扑

```
 ________                               ________             
|		 |----------- 0/0 ------------ |        |
|MLSW 1  |                             | MLSW 2 |
|________|----------- 0/1 ------------ |________|

```

##### 3. 实验步骤

-  步骤一，配置Trunk-link，创建VLAN；
-  步骤二，完成VTP配置，实现vlan的同步；
-  步骤三，接口划入VLAN；
-  步骤四，配置MSTP实例并将VLAN划入指定实例中；
-  步骤五，修改参数，指定根桥、根端口；


##### 4. 实验配置

注：自行完成前三个步骤！

```
步骤4:在MLSW1 和MLSW2 上创建对应的实例,把VLAN 映射进去

MLSW1-2
	spanning-tree mode mst                                                                                                                                                                                                
	spanning-tree mst configuration                                                                                            
	name cisco                                                                                                          revision 1                                                                                                      instance 1 vlan 1, 3, 5, 7, 9                                                                                             
	instance 2 vlan 2, 4, 6, 8, 10

此时完成如上配置后,请校验不同实例的根是谁,现象如下:

MLSW1#show spanning-tree root     

结果如下：                                                                                         
                                                                                                                           
                                        Root    Hello Max Fwd                                                              
MST Instance           Root ID          Cost    Time  Age Dly  Root Port                                                   
---------------- -------------------- --------- ----- --- ---  ------------                                                
MST0             32768 aabb.cc00.0100         0    2   20  15                                                              
MST1             32769 aabb.cc00.0100         0    2   20  15                                                              
MST2             32770 aabb.cc00.0100         0    2   20  15  


MLSW2#show spanning-tree root

                                                                                                                           
                                        Root    Hello Max Fwd                                                              
MST Instance           Root ID          Cost    Time  Age Dly  Root Port                                                   
---------------- -------------------- --------- ----- --- ---  ------------                                                
MST0             32768 aabb.cc00.0100         0    2   20  15   Et0/0                                                       
MST1             32769 aabb.cc00.0100   2000000    2   20  15  Et0/0                                                       
MST2             32770 aabb.cc00.0100   2000000    2   20  15  Et0/0   

如上现象表明,两个实例的根是MLSW1,MLSW2 的根端口是E0/0


步骤5:完成根的分配设置

MLSW1(config)#spanning-tree mst 1 root primary                                                                                                                                                               
MLSW1(config)#spanning-tree mst 2 root secondary

MLSW2(config)#spanning-tree mst 1 root secondary                                                                                                                                                             
MLSW2(config)#spanning-tree mst 2 root primary 

完成如上配置后,请再次校验不同实例的根,现象如下:

MLSW1#show spanning-tree root                     


                                                                                                                           
                                        Root    Hello Max Fwd                                                              
MST Instance           Root ID          Cost    Time  Age Dly  Root Port                                                   
---------------- -------------------- --------- ----- --- ---  ------------                                                
MST0             32768 aabb.cc00.0100         0    2   20  15                                                              
MST1             24577 aabb.cc00.0100         0    2   20  15                                                              
MST2             24578 aabb.cc00.0200   2000000    2   20  15  Et0/0   


MLSW2#show spanning-tree root                                                                         
                                                                                                                           
                                        Root    Hello Max Fwd                                                              
MST Instance           Root ID          Cost    Time  Age Dly  Root Port                                                   
---------------- -------------------- --------- ----- --- ---  ------------                                                
MST0             32768 aabb.cc00.0100         0    2   20  15  Et0/0                                                       
MST1             24577 aabb.cc00.0100   2000000    2   20  15  Et0/0                                                       
MST2             24578 aabb.cc00.0200         0    2   20  15   

步骤6:最终完成路径的优化

MLSW2 
	int e0/0
	spanning-tree mst 1 cost 20000000


```

##### 5. 排错与总结

典型的STP故障：

1. 双工不匹配
P2P链路的配置错误，有可能导致环路；

2. 单项链路故障

3. 数据帧损坏

4. 资源不足

5. portfast 配置错误

[参考资料]

[MSTP](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol)





























