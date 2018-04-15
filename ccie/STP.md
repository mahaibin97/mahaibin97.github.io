STP(spanning-tree protocol) 生成树协议
===========


## STP概述
> Spanning Tree Protocol（[STP](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol)）生成树协议是一项网络协议，它为以太网提供了一套避免环路的逻辑机制。

1. STP的基本功能是避免网桥环路以及广播消息的肆意泛洪。生成树允许一个网络结构中存在备份以及容错。

2. STP在网络架构的2层创建了“一棵树”，用来隔绝那些不属于该树的连接，保证任意两个节点之间都只有一条主链路。

3. STP的版本

|  协议          |  缩写  |  IEEE定义文档  | 
|  ----------    | ----   |   -----------  |
|  生成树协议    |  STP   |  IEEE802.1d    |
|  快速生成树    |  RSTP  |  IEEE802.1w    | 
|  多生成树协议  |  MSTP  |  IEEE802.1s    | 

4. 冗余拓扑
为了避免单故障点（比如链路，接口无备份）而提供的链路以及接口的备份，这就是冗余链路。

> 冗余链路带来了在解决单故障点问题的同时，也带来了许多的问题：
>
> 1. 广播风暴，由于交换机对广播流的防洪处理，造成的广播风暴；
> 2. 紊乱的MAC地址列表信息，这是交换机学习MAC地址的方式导致的；
（交换机端口接收数据，将数据帧，源MAC地址与接受接口关联，最终形成MAC地址列表条目。）
> 3. 数据的重复发送和接受。


## STP中的术语

1. 桥，bridge 即交换机；
2. 根桥，root bridge，参考点的作用（只有一个）；
	根桥是竞选出来的，所有的非根桥都要以根桥为参考点，计算出最短路径。
	最短路径负责转发数据，而其他路径对应的接口就被暂时性关闭。
3. 非根桥，no-root bridge 
4. 指定端口，DP通常位于根桥，有数据转发、MAC地址的学习能力；
5. 根端口，RP位于非根桥，根端口是抵达根桥的最短路径出口（只有一个）。
6. BPDU，bridge protocol data unit桥协议数据单元
	BPDU是一个交互消息，STP开启之后每间隔2s发送一次，STP的各种竞选都依赖于此消息包含的参数。

**补充**

我们可以这么说，
> 每一个VLAN只能有一个根桥；
每个非根桥只能有一个RP；
每一个网段只能有一个DP；
非RP与DP被STP阻塞掉；
Catalsy交换机每一个vlan运行一个STP，叫**PVST**(见后文)。

## STP的工作机制

###### 步骤1：交换网络中竞选出一个参考点：叫做根桥 root bridge
STP根桥是非根桥用来确定交换机互连接口哪些被阻塞，哪些处于数据转发状态。
非根桥保留的接口实际上是拥有抵达参考点最佳最短路径的接口。

**STP根桥的竞选过程：**
STP交换机默认开启，交换机互联接口会相互发送STP交互信息，用于竞选根桥。
BPDU：STP交互消息中含有BPDU(桥协议数据单元)。

> 交换机之间间隔2S发送一次。

BPDU中包含了BID（桥ID）字段，BID字段就是用于竞选根桥所用，其包含了两个内容：

- 1）优先级，默认32768
- 2）交换机芯片MAC地址，

交换机之间BID先比较优先级，越小越优；如果优先级相同，再比较MAC地址，也是越小越优。

**注意**， 

> 根桥选定后是可以被取代的。
根桥上所有本地接口都是处于学习和转发状态的。


###### 步骤2：非根桥上根端口（RP）的竞选
（根端口是非根桥上唯一处于激活状态的交换机互联端口）

根端口通过交换机间，交换BPDU，比较BPDU中的3个参数竞选而来。(根端口的竞选也是依次比较这三个参数)

1. COST开销 ：越小越好 ，带宽开销

  |  带宽   |  STPcost  |
  |  ----   |   ------  |
  |  10M    |    100    |
  |  100M   |     19    |
  |  1G     |      4    |
  |  10G    |      2    |

2. SENDER BID：发送者BID（发送者桥ID）
非根桥不同接口比较接收到的发送者BID，越小越优。

3. SENDER PID：发送者PID，实际上就是端口ID，是优先级128和端口ID组合。
非根桥接口比较各自接受到的对端的BPDU携带的PID，小则优。

**注意**
STP时刻监控着交换网络，一旦根端口出现了问题，那么STP可能就需要竞选新的根端口。

###### 步骤3：竞选指定桥
指定桥拥有的端口将成为指定端口，指定端口不会被阻塞，对端被阻塞。
谁抵达根桥的cost越小，谁是指定桥；
谁拥有的BID越小，谁是指定桥；

###### STP的重算
STP到的老化时间，该事件是BPDU发送间隔的10倍，20S。
根桥、非根桥一旦竞选成功，非根桥老化计时器开始倒计时，理想状态老化计时器会被来自于BPDU刷新，如果老化计时器超时，也没收到根桥的BPDU，那么STP开始放弃之前所有结果开始重算。

## STP中交换机、端口角色和状态

1. 交换机角色
根桥(bridge)、非根桥、指定桥(DB)

2. 端口的角色
指定端口(DP)：根桥和指定桥都有指定端口；
根端口(RP)：非根桥上的端口；

3. 端口状态
STP协议中，交换机端口的状态有5种：init、block、listen、learn、Foward。

> init 状态：初始化状态
1--2s后，进入下一个
Block状态：阻塞状态，该状态下交换机接口不去发送BPDU，无法转发用户数据，无法学习数据携带的MAC地址。
20S后，切换到下一状态
Listen状态：侦听状态，该状态下，交换机端口开始发送BPDU，开始根桥、根端口、指定桥竞选。
15s后
Learn状态：只有根端口，指定端口才有资格进入该状态，不然回到阻塞状态。					该状态下，交换机端口无法转发用户数据，但是开始进行MAC地址的学习。
15S
Forward 状态：转发状态，根端口、指定端口，才有资格进入，该状态下可以转发数据，同时学习数据MAC地址。



## STP的配置
思科交换机上默认启用了STP协议，所以不需要我们再次手动启用。但我们为了学习STP的相关知识，应该使用查看命令去校验STP结果。

```
1.获取交换机BID，确认根桥
  show spanning-tree bridge   //查看BID
  show spanning-tree root     //查看根桥BID
  show spanning-tree VLAN  <编号>  //应用于模拟器

注意!!!
优先级加了VLAN编号（32768+1=32769）

2.确认非根桥上的根端口
  show spanning-tree brief      （实物设备）
  Show spanning-tree vlan 1      （模拟器）

补充!!
需要改根桥的目的是，次优路径。
```

有时候，我们需要修改RB或者DR，则需要用到以下命令行：

```
优先级默认问128，更改时值为:16/32/64/128，小优。
修改优先级：
Switch(config)#spanning-tree vlan 1 priority 4096

修改开销：(接口下，针对VLAN来修改)
Switch(config-if)#speed <10/100/auto>
直接改带宽：bandwidth <1--1000 0000>
```

## PVST(per VLAN spanning tree)每VLAN生成树

每VLAN生成树，就是STP在VLAN中的实现，每个VLAN运行维护一棵“生成树”。它的原理机制和STP基本一致。

###### PVST+

1.portfast（快速端口，一般应用于接入层交换机），一旦设置为portfast，该端口将不参与根端口的竞选；
设置方式：
```
Switch（config）#spanning-tree portfast default  
//将本交换机所有端口设置为portfast，针对console口
Switch（config）#inter <接口>
Switch（config-if）#spanning-tree portfast disable
Switch（config-if）#spanning-tree portfast disable trunk //trunk 口
//针对具体接口
```
2. BPDU根保护，与portfast配合使用
	m接口变成down状态，进行自我保护；down状态不会自动开启，必须手动开启；

```
	Switch（config）#spanning-tree portfast bpduguard default //全局 状态下
			Switch（config-if）#spanning-tree portfast bpduguard enable //接口下开启保护
			Show spanning-tree //查看是否启用
```

3. 上行链路 uplinkFast




[参考文献]

1. Wikipedia/[Spanning_Tree_Protocol](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol)
2. RFC [7727](https://www.rfc-editor.org/rfc/rfc7727.txt)
