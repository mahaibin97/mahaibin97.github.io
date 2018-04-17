RSTP(rapid spanning tree protocol)
======

RSTP 快速生成树
在2001年，IEEE组织发布的802.1Q标准介绍了RSTP，快速生成树提供了更加快速的生成树机制，从而引入了新的收敛机制和桥端口的角色来完成快速生成树。
STP需要花费30到50秒的时间去收敛，而RSTP只需要花费3倍的hello消息发送的时间（默认：2秒3次）

##### RSTP的机制

1. RSTP增加了替代端口和备份端口，减少了生成树重算的时间；

2. 增加了边界端口 edge port，完全不需要计算，能立刻让端口处于转发状态；

3. TCN通告，可以快速通告逻辑拓扑的变化；


##### 端口角色

1. DP 指定端口，根桥上的所有端口都是指定端口，

2. RP 根端口

3. ALT 替代端口

替代端口实际上就是根端口竞选后落败的端口，替代端口会监控根端口状态，根端口状态，根端口一旦故障，替代端口立刻成为新的根端口；

4. BCK 备份端口

备份端口也是在故障后加快重算的机制。

**注意**

> 正常状态下，ALT端口和BCK端口都是处于禁用状态的。 

##### 端口状态

|  RSTP             |  特点  |
|  -------------    |  ----  |
|  discorrding禁用  |  不计算拓扑，不转发数据，不学习MAC地址 15S  | 
|  LRN   学习       |  15 S  |
|  FWD  转发        |        |


##### 切换到RSTP模式

```
switch(config)#spanning-tree mode rapid-pvst

根的备份
MLSW1(config)#spanning-tree vlan 20 root secondary 
```


##### RSTP的链路类型

- link type，RSTP通过link type 判断边界端口是否生效；
- P2P，点到点的链路类型，边界端口生效（接口全双工）；
- share，共享的链路类型，边界端口失效，接口参与RSTP计算（半双工）；

> 【链路类型是基于双工的询问和是否收到询问消息】


##### STP与RSTP的TCN与TCA—BPDU

BPDU分为[配置]config-BPDU（配置BPDU，用来计算无环拓扑）；
又TCN/TCA BPDU（拓扑改变通告/拓扑改变确认BPDU）。

- TCN用来通告拓扑的改变；
- TCN用来确认拓扑的改变，并且要求接受者刷新MAC地址列表，防止记录无效的MAC地址信息；
- STP中非根桥会间隔2s发送一次TCN，只有当根桥才可以发送TCA，终止TCN的发送；
- RSTP非根桥也会间隔2s发送一次TCN，但是所有收到的TCN的交换机都有资格发送TCA,终止TCN的发送。

##### RSTP的配置