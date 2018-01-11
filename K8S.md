# k8s笔记
## cidr

无类别域间路由（Classless Inter-Domain Routing、CIDR）是一个用于给用户分配IP地址以及在互联网上有效地路由IP数据包的对IP地址进行归类的方法。

CIDR的最大的好处就是可以进行前缀路由聚合。例如， 16个原来的C类（/24）网络现在可以聚合在一起，对外显示了一个/20的网络了（如果这些网络的的地址前20位都相同）。两个对齐的/20网络又可进一步聚合为/19，依此类推。这有效地减少了要对外显示的网络数，防止了路由表爆炸，也遏制了互联网进一步扩大。

## ip allocation design

当启动时，节点被告知子网所有分配的IP范围。每个节点必须具有相同的范围。

我们通过领导选举的过程来处理并行启动。从本质上说，具有最高id的节点为自己声明了整个范围，然后其他节点可以开始请求它的子空间。

当一个节点启动时，它会将CRDT初始化为一个空的环。然后等待更新或命令。在节点接收到分配或声明IP地址的命令之前，它并不关心发生了什么，但它需要跟踪它。

当一个节点第一次接收到这样的命令时，它会参考它的map。如果它看到任何其他节点声称有一些保留，那么它将继续正常分配。否则，如果没有收到这样的更新(例如，我们要么没有收到更新，要么只更新了所有节点条目为空)，那么该节点将启动一个领导人选举。如果该节点具有最高的id，那么该节点就声明整个IP范围本身，在环的开头插入一个标记。

如果它看到另一个节点具有最高的ID，它会向该节点发送一条消息，建议它是leader。接收这样一条消息的节点将按照上面的方式进行:只有当它看到没有其他节点时，它才会将自己视为具有最高ID的节点。

请注意，所选的leader可以是一个具有全局最高id的节点，在事件中，如果一个节点具有更高的id，那么它将作为另一个节点做出成为leader的决定。这是好的;新节点本身不会声称自己是leader，直到某个时间过去了，有足够的时间从领导者那里获得更新，从而抢占先机。

Failures::

两个没有互相沟通的节点，每个人都可以决定成为领导者->这是一个根本的问题:如果你不了解别人，你就不能为他们提供补贴。产生的情况实际上是一个网络分区，当节点进行接触时，可以以同样的方式解决。为了缓解这个问题，节点可以开始在一个随机的点上分配IP，以最小化在合并它们的映射之前分配相同IP的机会。

在发送map->之前，潜在的领导者就会死亡，这种失败将被底层的Weave对等拓扑发现。需要空间的节点将重新尝试，在所有连接的节点上重新运行领导选举。

## weave-npc
Weaveworks Kubernetes Network Policy Controller(weave k8s 网络策略控制器)
## kube-peers
在weaver启动之前，kube-peers会向k8s获取所有已经启动的weave node列表,并以参数方式传递给weaver 
## weave与k8s
## weave
weave网络共有3中工作模式

Bridge,此模式下pcap捕获发送给weave的网络包，然后交给weave,weave判断是本节点的就直接发送，否则根据peers找到对应weave所在的node发送出去，对方node利用pcap做类似操作完成网络包投送

```
                 +-------+
(container-veth)-+ weave +-(vethwe-bridge)--(vethwe-pcap)
                 +-------+
```
BridgedFastdp是在Bridge上的改进,利用操作系统提供的Open vSwitch技术取代了weave node间的连接，更加高效

```
                 +-------+                                    /----------\
(container-veth)-+ weave +-(vethwe-bridge)--(vethwe-datapath)-+ datapath +
                 +-------+                                    \----------/
```
Fastdp,"weave" is an Open vSwitch datapath, and capture/injection are as in
BridgedFastdp. Not used by default due to missing conntrack support in
datapath of old kernel versions (https://github.com/weaveworks/weave/issues/1577).

```
                 /-------\
(container-veth)-+ weave +
                 \-------/

```

## weave与calico
BGP(Border Gateway Protocol,边界网关协议)是互联网上一个核心的去中心化自治路由协议,大多数互联网服务提供商（ISP）必须使用BGP来与其他ISP创建路由连接（尤其是当它们采取多宿主连接时）。calico使用BGP协议在每个容器所在的节点创建一个虚拟路由器，来完成跨主机容器间的网络通信
## weave与flannel

## cni
CNI(Container Network Interface,容器网络接口)是一个云计算基础项目，它由一个规范和库组成，用于编写插件来在Linux容器中配置网络接口，以及一些支持的插件。CNI只关注容器的网络连接，并在删除容器时删除已分配的资源。因此，CNI有广泛的支持，并且规范很容易实现。
## cni与k8s
* kubernetes 先创建 pause 容器生成对应的 network namespace
* 调用网络 driver（因为配置的是 CNI，所以会调用 CNI 相关代码）
* CNI driver 根据配置调用具体的 cni 插件
* cni 插件给 pause 容器配置正确的网络
* pod 中其他的容器都是用 pause 的网络
