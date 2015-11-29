#小型企业网设计与模拟
##设计背景
* 该企业拥有三层办公楼
	* 一楼：业务部、设计部、管理部
	* 二楼：财务部、测试部、管理部
	* 三楼：总经理办公室、管理部
* 各部门存在访问限制
	* 测试部和设计部可相互访问
	* 管理部可访问除财务部和总经理办公室以外的所有部门
	* 总经理办公室可访问所有部门
	* 其它部门不能相互访问
	
##拓扑图
![](https://raw.githubusercontent.com/jifaxu/image/master/main.PNG)

##路由器配置

![](https://raw.githubusercontent.com/jifaxu/image/master/router_config.PNG)
```
en
conf t

int fa1/0
ip add 192.168.1.2 255.255.255.0
no shutdown

int fa2/0
ip add 192.168.2.2 255.255.255.0
no shutdown

int fa3/0
ip add 192.168.3.2 255.255.255.0
no shutdown

int s0/0
ip add 10.0.0.1 255.255.255.0

no shutdown

int s0/2
ip add 20.0.0.1 255.255.255.0

no shutdown


exit
ip route 30.0.0.0 255.255.255.0 10.0.0.2

ip route 192.168.4.0 255.255.255.0 10.0.0.2
ip route 192.168.5.0 255.255.255.0 10.0.0.2
ip route 192.168.6.0 255.255.255.0 10.0.0.2

ip route 192.168.7.0 255.255.255.0 20.0.0.3
ip route 192.168.8.0 255.255.255.0 20.0.0.3
ip route 192.168.9.0 255.255.255.0 20.0.0.3

```
配置结果

![](https://raw.githubusercontent.com/jifaxu/image/master/iproute.PNG)
##交换机配置


##自反ACL
_*自反 ACL*_ 允许最近出站数据包的目的地发出的应答流量回到该出站数据包的源地址。这样您可以更加严格地控制哪些流量能进入您的网络，并提升了扩展_*访问列表*_的能力。
网络管理员使用自反 ACL 来允许从内部网络发起的会话的 IP 流量，同时拒绝外部网络发起的 IP 流量。此类 ACL 使路由器能动态管理会话流量。路由器检查出站流量，当发现新的连接时，便会在临时 ACL 中添加条目以允许应答流量进入。自反 ACL 仅包含临时条目。当新的 IP 会话开始时（例如，数据包出站），这些条目会自动创建，并在会话结束时自动删除。

```
ip access-list extended SJOUT1


  deny ip 192.168.4.0 0.0.0.255 any
  deny ip 192.168.1.0 0.0.0.255 any
    
  permit ip  192.168.3.0 0.0.0.255 192.168.2.0 0.0.0.255 reflect REF 
  permit ip  192.168.6.0 0.0.0.255 192.168.2.0 0.0.0.255 reflect REF 
  permit ip  192.168.7.0 0.0.0.255 192.168.2.0 0.0.0.255 reflect REF 
  permit ip  192.168.8.0 0.0.0.255 192.168.2.0 0.0.0.255 reflect REF 
  permit ip   any  192.168.2.0 0.0.0.255
ip access-list extended SJIN1
  evaluate REF
  deny   ip   192.168.2.0 0.0.0.255 192.168.3.0 0.0.0.255 
  deny   ip   192.168.2.0 0.0.0.255 192.168.6.0 0.0.0.255
  deny   ip   192.168.2.0 0.0.0.255 192.168.7.0 0.0.0.255
  deny   ip   192.168.2.0 0.0.0.255 192.168.8.0 0.0.0.255
  permit ip   192.168.2.0 0.0.0.255 any
int fa2/0
 ip access-group SJIN1 in
 ip access-group SJOUT1 out
```

##测试结果
###业务部
![](https://raw.githubusercontent.com/jifaxu/image/master/yw.PNG)
###测试部
![](https://raw.githubusercontent.com/jifaxu/image/master/cs.PNG)
###管理
![](https://raw.githubusercontent.com/jifaxu/image/master/gl.PNG)
###办公室
![](https://raw.githubusercontent.com/jifaxu/image/master/boss.PNG)

