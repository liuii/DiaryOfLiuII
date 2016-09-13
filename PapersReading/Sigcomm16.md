# SigComm16论文阅读手记   

#### `G13P02` `2016-09-13` A Transparent Highway for inter-Virtual Network Function Communication with Open vSwitch  
- `Open vSwitch`即开放虚拟交换标准，为网络管理员提供虚拟VM之间或内部的流量可见性和控制。本质上就是用软件来实现的虚拟交换机，再将虚拟机与虚拟交换机进行连接。  
- `Data Plane Development Kit, DPDK`是用于快速包处理的一组数据平面库和网络接口控制驱动，[DPDK](http://dpdk.org)。  
- **==小结==**  
- 通常某些复杂的服务会被重新分解为多个独立的虚拟网络功能（NFVs），而这些NFVs通常会被部署在同一台物理服务器之上的独立虚拟机中，这些虚拟机通常使用虚拟交换进行连接。  
- 本文提出了一种优化的方案，通过为需要互相通信的虚拟机之间创建一个绕过vSwitch的点对点逻辑链路，来提高虚拟机之间通过vSwitch进行分组交换的透明度和动态性。  
- **透明性**指的是，一个利用本文方案的应用不需要做出任何改变，对于一个附加在vSwitch上的Openflow控制器不需要注意到这些改变。  
- **动态性**指的是根据对OpenFlow规则的分析，可以为VMs返回`VM-VM`或者`VM-vSwitch-VM`的链路。  

----
#### `G13P01` `2016-09-13` A 60Gbps DPI Prototype based on Memory-Centric FPGA  
- `Deep Packet Inspection, DPI`也被称为`Complete Packet Inspection, CPI`、`Information eXtraction, IX`。通过对数据包的数据部分进行检测（有时也对头部进行检测），来发现不符合协议的分组、病毒、垃圾邮件、入侵，或者定义标准来决定如何处理该分组（放行或发送到其他目的地），或者是出于收集统计信息的目的。  
- `Aho-Corasick algorithm`是用于`String Pattern Matching, SPM`中的字符串搜索算法。  
- `Snort`和`Emerging Threats`是两个常用的DPI测试字典。  
- **==小结==**  
- 目前的DPI架构当中，主要利用Aho-Corasick Algorithm来创建一个高效的有限自动机，而其瓶颈是内存的速度。  
- 本文实现的系统，包含48个并行引擎和两层的内存架构。一个辅助的多核CPU用来根据字典生成确定性有限自动机（`deterministic finite automaton, DFA`）。  
- 两层内存结构中的片内RAM用来存储一小部分的`State Transition Table, STT`，而外部的大容量DRAM则用来存储完整的STT。文章发现经过优化在片内部分只存储256个State就可以达到非常低的`Cache Miss Rate`。  

----
#### `G12P21` `2016-09-12` Taming the Flow Table Overflow in OpenFlow Switch  
- `OpenFlow Switch, OFS`是支持OpenFlow的交换机。  
- `Content-Addressable Memory, CAM`与传统的存储器的差别在于，传统的存储器通过给出地址，然后返回数据。而`CAM`则是给出数据返回地址或者地址列表。  
- `Ternary Content-Addressable Memory, TCAM`与`CAM`的差别在于：`CAM`的搜索数据只能包含`1`与`0`。而`TCAM`则支持第三种匹配`X`，表示对此位不关心。因此被广泛的用于流表的检索当中。  
- `Quad data rate, QDR`是一种四倍速的数据传输方式，通过对时钟信号进行90度的相移，这样两个信号可以得到两个上升沿和两个下降沿，所以在一个时钟周期内可以传送四次数据。  
- `Static random-access memory, SRAM`比`DRAM`速度快、密度低、成本高的存储芯片，包括前两种都属于快速存储器，被广泛的用于OFS的流表当中。  
- `pipeline-able`是OpenFlow中用来处理分组的流水线，通常由多个流表构成，每个流表包含多个条目。  
- **==小结==**  
- 本文的核心在于介绍一种用来改良流表溢出的方法。  
- 尽管在OFS中广泛的应用TCAM/QDR/SRAM等高速内存来构成流表，但是高速内存的发展落后于网络的需求。在OFS的流表有很大的可能产生溢出，从而导致OFS与其控制器之间产生大量的分组。  
- `Flow Table Sharing, FTS`是本文提出的用来缓解流表溢出的方法，其核心思想是将任务繁重的交换机中无法命中的流表的分组，转发到工作负载较轻的交换机上。
- 这个方案存在两个需要解决的问题：如何让SDN交换机随机的选择一个正确的端口，如何在不改动硬件的情况下，让这个`过程`在通用的SDN交换机中是`pipeline-able`的。  
- `FTS`利用在流表中添加一个`flow missed`的条目，从而将flow交给一个`SELECT`组，由包含在组中的出口选择算法来决定转发的端口，算法如下：  
``` C
init outport = 0
// packet arrival
if not overflow
	outport = CONTROLLER;
else if overflow
	outport = (outport + 1) mod N;
// avoid loop
if outport == inport
	outport = (outport + 1) mod N;
return outport;
```
- 该算法直到`flow-table`有了空间可以添加新的流表入口，才将目的端口设置为`CONTROLLER`。  
- 该算法的测试与评价在`MiniNet`平台上完成。  

----
#### `G12P20` `2016-09-09` \* TafLoc: Time-adaptive and Fine-grained Device-free Localization with Little Cost  
- `device-free localization, DfL`不需要额外设备的定位，例如利用无处不在的WiFi信号的`Received Signal Strength, RSS`信息来进行定位。  
- **==小结==**  
- 目前利用WiFi信号的RSS来进行定位的指纹方法，但是这种方法即使在环境没有任何变化的情况下也非常的慢，此外频繁的更新每个定位点的RSS也需要大量的人力花费。  
- `DfL`方法不需要目标物携带任何设备，将物体置于每个位置，然后记录其各接收器的信号强度作为指纹。当目标进入房间后，对指纹进行比对，然后获得其位置。但是RSS指纹会过期，所以需要不断的更新，这会带来人力成本和时间成本的支出。  
- 指纹库可以表达为具有如下性质的一个二维矩阵，通过提取Reference矩阵和Largely Distorted矩阵，来减少更新的时间。  

----
#### `G12P19` `2016-09-09` SLA-NFV: an SLA-aware High Performance Framework for Network Function Virtualization  
- `Service-level agreement, SLA`是指提供服务的企业与客户之间就服务的品质、水准、性能等方面所达成的双方共同认可的协议或契约。  
- **==小结==**  
- `SLA-NFV`是利用了软件和可编程硬件的混合式基础架构，用于增强NFV遵守SLAs的能力,比纯软件实现服务链降低了超过60%的延迟。  
- 该原型是基于`OpenStack`和`NetFPGA`实现。  
- `SLA-NFV`的架构分为三层：  
- `1.` `Orchestrator`用来执行服务链，并决定VNF的部署、销毁和迁移。为了实现这一目标，`Orchestrator`周期性的向`NF Managers`查询最近的VNFs的性能。`Step1`实现延迟需求，使用贪心策略来快速找到最低延迟的路径。`Step2`实现吞吐量的需求，检查最快路径中每个实例的吞吐量，如果某些实例不能达到要求，则标记为*infinite*，然后重新执行`Step1`。算法结束于两种状态：找到了合格的路径和没有合格的路径，对于后者则需要部署新的实例。  
- `2.` `NF Managers`用来控制NF的生命周期。  
- `3.` 通过结合`programmable SDPA`硬件和`NFV`软件来实现高性能和灵活性。为了实现这一目标，本文为SDPA扩展了状态相关的接口以便迁移各种类型、各种流的状态。  

----
#### `G12P18` `2016-09-09` Rethinking the Design of OpenFlow Switch Counters  
- `OpenFlow`是一种通过网络访问交换机或路由器转发层的通讯协议。  
- `ONetSwitch`是叠锶公司作为全球首款基于Zynq器件实现的OpenFlow Switch产品，作为理想的SDN教育科研平台，具备“软件可编程，逻辑可重构，硬件可扩展”能力，是面向SDN/OpenFlow的可编程交换机。  
- `OFS`是OpenFlow Switcher。  
- 在网络设备里面区分不同的路径是一个很自然的选择，因为网络设备的首要任务是转发网络包，不同的网络包，在设备里面的处理路径不同。  
- `fast path`就是那些可以依据已有状态转发的路径，在这些路径上，网关，二层地址等都已经准备好了，不需要缓存数据包，而是可以直接转发。  
- `slow path`是那些需要额外信息的包，比如查找路由，解析MAC地址等。
- `first path`是设备收到流上第一个包所走过的路径，比如tcp里面的syn包，有些实现把三次握手都放到first path里面处理，而有些只需处理syn包，其他包就进入fast path的处理路径。  
- 在NP或者ASIC里面也需要区分`slow path`和`fast path`，`fast path`上的包都放在`SRAM`里面，而`slow path`的包放在`DRAM`里面。  
- 不过这也非绝对。决定处理路径的是对于速度的考虑。处理器的速度，内存的速度等。访问内存的速度由速度和带宽两个因素决定。因此，把`SRAM`放到`fast path`上，也是很自然的选择。  
- 系统的性能要从两方面考虑，硬件的设计和软件的设计，这两个方面的配合或者是分工，决定着系统的性能。  
- **==小结==**  
- 本文旨在解决在交换中通过使用CPU Cache来减少内存的使用。本文提出了`CACTI`（CAche CounTIng）以达到近乎0内存占用。  
- 本文分别在`ONetSwitch`和`FPGA-based PCI Express`平台上进行了实现。  
- 本文的核心内容为OFS的Counter设计。  

----
#### `G12P17` `2016-09-08` Privacy-Aware Infrastructure for Managing Personal Data  
- `Service-level agreement, SLA`设备级许可。  
- **==小结==**  
- 本文提出了一种位于家庭中的隐私数据许可设备。本文提出的内容是计算机与社会学科的解决方案，起因是欧盟希望能够通过法律来保护用户的个人隐私。  

----
#### `G12P16` `2016-09-08` PieBridge: A Cross-DR scale Large Data Transmission Scheduling System  
- `DR`：Datacenter Region  
- `IDC`：Internet Datacenter  
- `residual network`是网络流当中的一个概念。  
- **==小结==**  
- 在现有的数据中心的数据同步中，存在着以下问题：  
- `1.` 数据同步需要大量的带宽，而服务器的接口带宽在数据中心之间是有限的。  
- `2.` 数据传输对时间敏感，而当前的网络无法支持这种实时的要求。  
- 本文提出的`PieBridge`是一个广域网范围的数据传输中心平台。它通过高效的调度选择传输源来降低数据同步的完成时间。  
- `PieBridge`的调度算法在每一个数据传输期间包括三个过程：选择子任务、最大传输调度和子任务合并：  
- `1.` 首先将一个传输任务分解为多个子任务队列。  
- `2.` 用最大流算法（增广路径）进行调度。  
- `3.` 对拥有相同的源与目的的子任务进行合并，以减少下一次调度的运算量。  

----
#### `G12P15` `2016-09-08` Performance Evaluation of Locator/Identifier Separation Protocol through RIPE Atlas  
- `Locator/Identifier Separation Protocol, LISP`（RFC6830）。在目前的IP协议架构中，`IP地址`被用作命名空间，并用于两种不同的目的：  
- `1.` 做为`End-Point`的`Identifier`提供在当前本地网络寻址环境中的唯一的标识。
- `2.` 作为拓扑的`Locator`用于路由目的。  
- `LISP`协议试图分离这两者，并改进网络的扩展性、支持数据中心中的虚拟机迁移、IPv6过渡、增强域内传输工程。  
- `Stub network`是一个非正式的术语，用来描述只有一条逻辑链路接入到互联网当中。  
- **==小结==**  
- 本文是对LISP基础设施的性能测量。  
- LISP从语义学上将IP地址分为两个分类：`Rout- ing LOCators, RLOCs`用于因特网的核心；`Endpoint IDentifiers, EIDs)`则用于`Stub Network`。  
- `Stub Network`内部的通讯使用`EIDs`进行传统的路由和转发。  
- 域间的通讯需要一个额外的`map-and-encapsulate`操作——将使用`EID地址`的分组封装为使用`RLOC地址`的分组，从而将`EID`映射至`RLOCs`。  
- 在*LISP*与*non-LISP*网络之间通信的实现需要一种新的网络设备：*`PxTR`*。它包括两张类型的代理（*proxy*）：`Proxy Ingress Tunnel Routers, PITRs`和`Proxy Egress Tunnel Rou- ters, PETRs`。  
- `PITRs`广播LISP节点的`EID前缀`，以便`非LISP节点`可以访问它们。同时`PITRs`用他们自己的`RLOCs地址`将`非LISP分组`进行封装并路由至`目的RLOCs`。  
- `PETRs`允许`LISP节点`发送分组至`非LISP节点`。`PETRs`将`LISP分组`进行解封，将其转换为传统的`IP分组`并将其转发至传统的互联网。  
- 文章中利用几个不同的探针节点，利用ping测量了LISP网络与现有网络之间的差别。  

----
#### `G12P14` `2016-09-08` PathCache: A Path Prediction Toolkit  
- `RIPE Atlas`和`CAIDA’s Ark`都是网络测量平台。  
- `PathCache`允许用户重新使用traceroute对网络路径的测量结果。  
- 本文使用了`CAIDA’s Ark`、`RIPE Atlas`和`iPlane`平台的*traceroute*数据。  
- `BGP`：边际网关协议。`BGPStream API`是本文使用的`BGP`监控器。  
- `Best-effort`指尽力而为，意味着该网络不给于任何承诺，TCP/IP就是一种尽力而为的网络。  
- **==小结==**  
- 本文提出的*PathCache*通过先验数据集来预测路径，因此研究者不需要等待测量结束就可以得到一条测量路径。  
- 常用的路径预测方法中，当需要计算大量路径或无法直接测量路径的时候，通常会使用算法仿真。但是这种方法的精度无法满足那些对安全敏感的应用，例如`Tor`的客户端（因为这种协议的目的就是尽量的避免被窃听）。`PathCache`不需要客户端发送它自己的探测结果，而且它带有的元数据和数据源能够对预测的路径进行有效性的验证和评估。  

----
#### `G12P13` `2016-09-07` \* Named Data Networking Based Smart Home Lighting  
- `Named Data Networking, NDN`是一个研究物联网的架构平台，替代原有通讯中关注于`where`，改为关注于`what`。[`项目地址`](https://named-data.net)，包括架构与实验平台等。  
- `Information Centric Networks, ICNs`以信息为中心的网络，NDN就是一种ICN。  
- `Push`与`Pull`两种模式的差别：前者服务器占据主动，后者客户端占据主动。  
- `Forwarding Interest Base, FIB`是NDN用作路由表的一种数据结构，用来保存对外接口与名字之间的映射。  
- **==小结==**  
- 这是一篇涉及**物联网**的文章。  
- NDN为了实现基于带状态转发的request/response架构，使用了两种类型的分组：`Interest`和`Data`。这两种类型的分组都提供`URI-like names`。这两种数据包内在支持`Multicast`。并且去除了数据获取与数据位置的耦合度。  
- 本文提到的家庭智能照明系统包括：  
- `Home Router`用来连接所有IoT设备的无线路由器。  
- `Smart Contrller`是带有无线接口（802.11g）IoT平台（树莓派）。  
- `Light Node`是一个传统的灯泡，连接到一个智能控制设备，可以打开或关闭他。  
- `Occupancy Detector`是一个运动传感器，用来跟踪人的移动（出入房间）。  
- `Luminosity Detector`是一个亮度传感器，用于测量室内亮度。  
- `Smart Home Controller`是运行在智能控制器上的应用，基于房间的人员情况以及亮度来控制灯光。  
- 这套系统使用的为：`Raspberry Pis`（Raspbian Wheezy）和`router`（OpenWrt 12.09）。NDN转发守护进程使用的是[`NFD`](http://named-data.net/doc/NFD/current/)。端点用Python开发，使用了`PyNDN`客户端库。  
- 该系统具有以下特点：基于NDN Push的通讯；基于名字的多路组播；FIB优化。  
- 该系统通过使用`Interest Filtering`对FIB进行了优化。  

----
#### `G12P12` `2016-09-07` Modular SDN Compiler Design with Intermediate Representation  
- `SDN`的功能就是把控制平面和数据平面去耦，控制平面使用高级语言进行控制，数据平面使用低级规则来进行控制。  
- `Frenetic`，`Maple`，`Merlin`，`P4`是现在使用的控制平面高级语言。  
- `OpenFlow`，`PoF`，`ACI`是目前使用的数据平面低级规则。  
- `IR`（`Intermediate Language`）是编译中的一种技术，提供一种中间语言来对计算机程序进行分析。  
- `RYU`是一种基于组件的SDN框架，项目主页：[`RYU`](https://osrg.github.io/ryu/)  
- **==小结==**  
- 目前的SDN基本上都是一种高级语言针对一种低级规则进行编译。这种情况存在两个缺点：  
- 第一，不同的SDN平台之间无法混用，同时很多优化技术只能针对某一种SDN服务，例如：`VeriFlow`优化技术只能应用于`OpenFlow rules`。  
- 第二，在数据平面，不同的顶级控制语言可能会造成冲突，简单的合并规则有可能会失去程序原有的意图。
- 因此本位提出了`Semantic Rule, SR`（类似于编译中的IR），作为中间层规则。所有的SDN程序首先在前端编译为`SRs`，然后在中间层对其进行优化，优化之后在后端编译为底层规则。  
- SR被定义为一个三元组：`<Scope, Constraint, Degree of Freedom (DOF)>`。  
- `Scope`用来指定SR操作的资源，类似于`OpenFlow Rule`中指定的`Match Fields`。  
- `Constraint`指定`Scope`之上的`Action`，并关联元组`(guard, action)`其中*guard*表示条件的限定（例如跳数、延迟等要求），如果满足*guard*则执行*action*。  
- `DOF`则用来指定`Constraint`的可更改的程度。  
- 编译过程：
``` Java
//Program Intent
Connect A and D on f in 3 hops, while traversing C

//Semantcs Rules, forbid used to avoid loop
A f, FORWARD 3 {h<3; h=h+1}, towards C and forbid A
C f, FORWARD 2 {h<3; h=h+1}, towards D and forbid C 
D f, FORWARD 2 {h<3; h=h+1}, forward 2

//Rules
A f, FORWARD 3; 
C f, FORWARD 2; 
D f, FORWARD 2
```

----
#### `G12P11` `2016-09-07` Magellan: Generating Multi-Table Datapath from Datapath Oblivious Algorithmic SDN Policies  
- `Datapath-Oblivious`是一种无感知的数据通路。  
- `OpenDaylight`和`Floodlight`都是目前主流的`SDN`控制器。相关的技术资料在[`SDNLAB`](http://www.sdnlab.com)上很多。  
- `datapath-oblivious algorithmic policies`可参考论文：`Maple: Simplifying sdn programming using algorithmic policies`。  
- **==小结==**  
- 本文重点讲述了两个新的本质算法：`map-explore`和`table-design`。  
- `map-explore`利用通用编程语言实现了一种新奇高效的形式，混合了`symbolic（map）`与`direct（explore）`，用以实现`a multi-table oblivious program`。并产生一种叫做`explorer graph`的数据结构。  
- `table-design`用来分开数据流图的程序，和合并表的工作。  
- 为了实现其目标`Magellan`引入一套复杂的编译器（*Compiler*）和运行时（*Runtime*）系统。
- AP（`Algorithm Policies`）实例：  
``` Java
// Program: Static-Example
onPacket(p) {
x = macSrc;
if (x > 4) {y = hostTable[macDst];} else {y = 1;} 
 egress = [2 * y]; }
```
- 将控制结构转化为跳转指令后：  
``` ASM
L2: cjump (macSrc > 4) L3 L5
L3: y = hostTable[macDst]
L4: jump L6
L5: y = 1
L6: egress = [2 * y]
```
- 加入数据依赖关系后：  
``` C
L1: g = (macSrc > 4)
L2: if g: y = hostTable[macDst]
L3: if !g: y = 1
L4: egress = [2 * y]
```
- 最终生成数据流图。  

----
#### `G12P10` `2016-09-01` Horse: towards an SDN traffic dynamics simulator for large scale networks  
- `Source Routing`可以为分组选择需要经过的部分或全部路由器，相关阅读：`DSR协议`。  
- [`MiniNet`](http://mininet.org)是网络实验的虚拟平台。但是这个平台缺乏弹性，对巨型网络拓扑结构或巨大的负载的分析缺乏足够的支持。  
- `OpenFlow`是一种网络层协议。`OpenFlow`交换机将原来完全由交换机/路由器控制的报文转发过程转化为由`OpenFlow交换机`（`OpenFlow Switch`）和`控制服务器`（`Controller`）来共同完成，从而实现了数据转发和路由控制的分离。控制器可以通过事先规定好的接口操作来控制`OpenFlow交换机`中的流表，从而达到控制数据转发的目的。  
- **==小结==**  
- 文章提出了一种新的网络实验仿真环境`Horse`，其主要的特点就是拥有可扩展的能力。  
- `Horse`仿真器的架构分为两层：`数据平面`和`控制平面`。  
- `数据平面`包含网络的`拓扑`、`事件`（例如链路失效、流，这些会被用于更新网络的拓扑）、`传输统计和网络状态`。  
- `控制平面`包括`监控器`（用来监控`数据平面`的传输统计与网络状态、）、`策略生成器`（根据用户输入的策略配置文件来生成控制策略）、`指令集`（执行策略，控制`数据平面`的网络拓扑）。  
- 策略配置文件如下所示：  
``` Javascript
"load balancing" : "edge->core",
"application based peering" : "e1->e3" : "http",
"rate limiting" : "e2->e4" : "500 Mbps"
```

----
#### `G12P09` `2016-09-01` FAST: A Simple Programming Abstraction for Complex State-Dependent SDN Programming  
- 很多的网络控制平面的计算依赖于`网络的状态`，例如路由算法中的`最短路径`依赖于`网络的拓扑`，`QoS`依赖于`拓扑`和`资源分配`，`安全功能`依赖于配置的`安全策略`。  
- `OpenDaylight`是由Linux基金会主持的开源协作平台。其目标是为了推动SDN、NFV的创新实施。  
- `ONOS`也是一个`SDN`框架，[`ONOS`](https://wiki.onosproject.org/display/ONOS/Wiki+Home)  
- **==小结==**  
- `FAST`是`Function Automation SysTem`的缩写，该系统是本文提出的一种用于解决`SDN`中状态依赖的问题。该系统主要解决两个问题：`对状态依赖进行自动跟踪`与`高效的重新执行调度`。  
- 例如在部署了`Dijkstra`路由算法的网络中，如果没能订阅某个可达链路的的状态，可能会导致计算得出的路径与网络拓扑不一致。所以现在的方法就是简单的过度订阅，订阅拓扑中每一个链路的状态变化，这会导致没有必要的重新计算。  
- 在某个QoS路由中，如果为某条路径保留了带宽，当改路径不可用的时候，需要首先释放保留的带宽，再寻找新的路径，否则会产生类似于“内存泄漏”式的带宽保留。在原始方法中，释放带宽的保留将会产生一个连锁式的反应。  
- 所以本文提出的方式就是把`State Changes`对开发者`透明`，由`FAST`来自动完成`状态依赖的跟踪`和`调度的重新执行`。  

----
#### `G12P08` `2016-09-01` Efficient Remapping of Internet Routing Events  
- `Routing Events`包含：路由器重新配置、链路失败、软件错误以及调度维护等。  
- `DTRACK`是一种用来预测与跟踪互联网路径变化的工具，详细描述可以参看文章：`DTRACK: A System to Predict and Track Internet Path Changes`。  
- `PlanetLab`是用来研究计算机网络与分布式系统的实验平台，它包含一组可用的计算机。只有带有`PlanetLab`节点的大学和企业才可以使用。但是在`PlanetLab`中也有一些免费的公共服务：`CoDeeN`、`Coral Content Distribution Network`可用。  
- **==小结==**  
- 现有的路由事件处理机制会导致两种后果：`(1)`路由信息过期，那些由于路由事件受到影响的其他路径的重新映射会延迟。`(2)`无法观测一个路有事件的影响范围，因为在重新映射受该事件影响的路径之前，可能会有另外一个路由事件出现。  
- 本文提出当检测到路由事件后，计算路由事件变化的范围，然后对此范围可能影响的路径进行更新。  

----
#### `G12P07` `2016-08-31` Conan: Content-aware Access Network Flow Scheduling to Improve QoE of Home Users  
- `QoE`（`Quality of Experience`）是用户对某种服务体验的度量，`QoE`尤其关注用户体验（`User Experience`）。`QoE`与`QoS`不同，后者试图从提供者（`Vendor`）的角度对服务质量进行客观的测量，而前者则是从使用者（`Custom`）的角度进行主观的测量。  
- 以网络服务为例`QoS`通常关注于`BandWidth`，而`QoE`则关注应用端的`Response Time`。  
- `Downstream`和`Upstream`分别指终端用户的`下行`和`上行`，通常对于家庭用户来说`Downstream`远大于`Upstream`，对于商业用户来说`Downstream`和`Upstream`基本持平。  
- `HLS`（`Http Live Streaming`）是Apple公司实现的一种利用`HTTP传输`传送视频数据的协议。每个视频都被分割为小的片段，每个片段并提供四种不同码率（`BitRates`）对应不同的分辨率。  
- `linux-htb`是`Linux QoS`中用来控制流量的算法。  
- **==小结==**  
- 本文聚焦于终端用户对网络的体验，从服务提供方来说分配的带宽是用来进行`QoS`的指标，但是对于用户来说，其体验是非常主观的。例如用户对在线视频的流畅度非常敏感，而对P2P下载速度的敏感度较低。  
- 因此，为了提高用户的体验度，就需要对不同的应用分配不同的带宽，以保证用户的体验。`基于内容感知的数据流调度`就是提高用户体验的一个重要手段。  
- 本文在前人的工作基础上，将数据流的鉴定移动到家庭网关来进行执行，对于`HTTP`流直接进行鉴定，对于`HTTPS`流则需要一个`可信任的中间人代理`来进行鉴定。  
- 本文设计的架构分为：`Broker`和`Controller`。  
- `Broker`工作于家庭网关，负责鉴定数据流。当发现符合条件的数据流，则发送`Request`到`Controller`。  
- `Controller`收到`Requst`后，对其进行验证，在解决冲突后，将规则下发到路由器。  
- 文章还提及了对于视频流的执行规则，给出了一个动态调整分配的带宽，并使其向视频流的真实码率进行收敛。  
- 本文从**`QoE`**的角度提出了改善用户体验的网络调度方案，是一种新奇的视角。  

----
#### `G12P06` `2016-08-31` Building Application-Aware Network Environments Using SDN for Optimizing Hadoop Applications  
- `AAN`（`Application-Aware Aetworking`）是本文提出的一种用来管理地理分布的`Hadoop Cluster`网络传输的方法。  
- `SDN`把之前由硬件处理的`Control Plane`替换为用软件来进行处理。`SDN`有三个最大的优点：动态的管理、配置和分配资源；在独立的服务器中运行网络控制功能；可以方便对新的网络协议进行部署和测试。  
- 本文主要用到了`SDN`的以下功能：`network topology discovery`，`traffic monitoring`，`flow rerouting`和`loop avoidance mechanisms`  
- `GENI`（[`Global Environment for Network Innovations`](http://www.geni.net)）是一个用来进行计算机网络和分布式系统研究的平台。聚焦于`facility architecture`，`the backbone network`，`distributed services`，`wireless/mobile/sensor subnetworks`等方面。本文的实验是在这个平台上完成的。参看文章[Geni-global environment for network innovations](http://ieeexplore.ieee.org/document/4664143/?arnumber=4664143)。  
- `OVS`（`Open vSwitch`）是用软件来实现的`虚拟多层交换机`。  
- `iPerf`是一个常用的网络测试工具，它可以产生`TCP`和`UDP`数据流，并测试该网络的`吞吐量（Throughput）`。本文利用`iPerf`来生成流量。  
- **==小结==**  
- 本文研究的核心在于现存的`MapReduce`框架只考虑了`CPU`/`DISK`/`MEMORY`等资源，但是并没有考虑节点的`network`资源。  
- 本文通过利用`SDN`为`MapReduce`过程添加了对网络资源的考虑，从而提高了运算的性能。  

----
#### `G12P05` `2016-08-31` Best Effort Task Scheduling for Data Parallel Jobs  
- `DAG`（`directed acyclic graph`），有向无环图。用来描述数据分析工作的各个阶段，尤其是在工作在`Hadoop`和`Spark`这种大规模分布式集群上的数据分析工作。  
- `BETS`（`a Best Effort Task Scheduling scheme`）是本文提出的一种调度（`Scheduling`）方案。  
- `Terasort`是`Hadoop`中的一个排序作业。`PageRank`是`Spark`中的一个计算页面引用评级的算法。文中利用这两个算法来测量CPU的利用率。  
- `State Machine`是本文用来描述`task model`的一个成员，本文把每个任务划分为四个`State`：`schedule`、`input`、`compute`和`output`。每个状态都包含其对资源的需求（而需求来自于上一轮执行的情况和历史上同一应用程序的执行情况）。  
- `Duty Cycle`（`占空比`）是信号处理领域的一个概念，指信号激活时间占总时间的百分比。文章利用`CPU占空比`来描述任务对算力的需求，通常占空比`低`的任务可以把CPU`分享`给其他任务。  
- `Hadoop Yarn`是`Hadoop`的一个新的`MapReduce`框架。  
- **==小结==**  
- 目前的并行调度算法中的资源需求是静态测量并制定给任务调度系统的。通过测量可以把任务进行简单的分类：`CPU-intensive`和`IO-intensive`，这两类任务对资源的需求存在巨大的差别。  
- 本文试图解决两个问题：`任务模型的描述`和`对资源需求动态变化的任务进行调度`。  
- 本文利用`state machine`和`duty cycle`来描述`task model`。而在任务调度上提供了`粗粒度`和`细粒度`的两层调度方式。  
- 在`粗粒度`的层面：选取并未处于`input`状态的`slot`，这样就可以当前任务处于`compute`状态时，执行新任务的`input`。  
- 在`细粒度`的层面：必须保证当前任务与新任务的`CPU占空比之和`不超过`100%`。  
- 在`资源管理`层面：当前任务对资源请求的`优先级`高于新任务。  
- 本文外来的工作着眼于：`分布式的任务调度器（目前是中央的）`和`考虑其他资源需求（例如BandWidth）`。  

----
#### `G12P04` `2016-08-31` Application-specific Acceleration Framework for Mobile Applications  
- `TCP Acceleration`、`SPDY`和`Compression`是用来降低移动应用响应（`Response`）时间，提升用户体验的方法。其中`SPDY`是`Google`的一种开放式协议，主要利用了`Compression`、`Multiplexing`、`Prioritization`技术，来降低网页传输的时延。  
- `Extractocol`是一种用来自动化提取Android应用程序`应用层协议行为`的技术。这篇文章位于：[`ACM`](http://dl.acm.org/citation.cfm?id=2790003)。本文利用了这个技术来分析Android应用，`Extractocol`的输入是Android应用的二进制包`.pkg`，输出`Request`和`Response`的特征，特征是利用正则表达式（`Regex`）来表示的。另外`Extractocol`还会产生`Request`和`Response`之间的依赖关系。  
- `mitmproxy`是一个基于控制台的代理，可以用`pip install mitmproxy`来进行安装，官网位于[mitmproxy](https://mitmproxy.org)。本文利用它来实现的`proxy`实验平台。  
- **==小结==**  
- 本文的目的在于降低移动应用的响应时间，以提高用户体验。文章的思路为三步：  
- (1) 利用`Extractocol`对`Android App`进行分析，获取该`App`的`Request`和`Response`的正则化表达（`Regex`），以及这些`Request`和`Response`之间的依赖关系。  
- (2) 从这些相互依赖的`Request`和`Response`中，寻找可以预取的机会。  
- (3) 然后根据寻找的机会进行编程，本文目前是使用的静态编程的方法，人工处理App的`预存优化`。文中提到他们的下一目标是`自动化处理`Android App的优化，`自动生成代码`。  
- 相信这个类型的研究方向能给**开发组**的同学以启迪。  

----
#### `G12P03` `2016-08-29` Achieving Consistent SDN Control With Declarative Applications  
- `Prolog`：一种用于人工智能和计算机语言学的逻辑编程语言。下面是该语言实现的`Qsort`：  
``` Prolog
partition([], _, [], []).
partition([X|Xs], Pivot, Smalls, Bigs) :-
    (   X @< Pivot ->
        Smalls = [X|Rest],
        partition(Xs, Pivot, Rest, Bigs)
    ;   Bigs = [X|Rest],
        partition(Xs, Pivot, Smalls, Rest)
    ).
 
quicksort([])     --> [].
quicksort([X|Xs]) -->
    { partition(Xs, X, Smaller, Bigger) },
    quicksort(Smaller), [X], quicksort(Bigger).
```
- **==小结==**  
- 本文提出了使用申诉式语言作为`SDN`应用对`Flow`的要求。
- 本文提出了使用投票机制来使得方案能够满足更多的`SDN App`的需求，文章最后和`Static Priority`以及`Athensk`进行了比较。  

----
#### `G12P02` `2016-08-29` A Longitudinal Analysis of .i2p Leakage in the Public DNS Infrastructure  
- **I<sup>2</sup>P（**`Invisible Internet Project`）是匿名的网络层，有点类似于`Tor`。
- `EepSites`是依托`I2P`提供匿名服务的站点。  
- `.i2p`是工作于`Overlay Network`的`Pseudo top-level domain(TLD)`，访问`.i2p`需要浏览器向`EepProxy`发出请求，然后获得`I2P peer key`来进行传输。  
- `I2P`的细节问题可以访问：\[[ I<sup>2</sup>P ](https://geti2p.net/zh/)\]  
- `SLD`——`Second-Level Domain`。  
- **==小结==**  
- 本文对两个`DNS`根节点（`A`和`J`），进行了分析。在这两个节点中发现了大量的`I2P`域名被泄漏到这两个根节点上。  
- `I2P`是一种`Overlay Network`，在这个网络中的域名都要通过其特殊的域名解析服务`EepProxy`来进行解析。文章中对泄露出来的`I2P`查询与泄露出来的`Onion(Tor)`查询进行比对，得出了两种网络的差别。  
- 文章继而对`I2P`网络泄露的原因进行了推断，大致可以归为：  
- `(1)` `用户理解与配置错误`：用户直接使用正常的方式来访问`.i2p`站点，或者错误的配置了软件设置。  
- `(2)` `浏览器预读机制`：浏览器为了加快速度直接去预读，导致域名泄露。  
- `(3)` `恶意软件`：有很多恶意软件利用`I2P`网络和自己命令与控制服务器进行通讯。文章中发现了一些被`Dyre`（一种银行业的木马`Trojan`）泄露的域名请求。
- `I2P`网络的使用者国家排名：`Russia`、`Unite States`、`China`。  

----
#### `G12P01` `2016-08-29` A First Look into Transnational Routing Detours  
- `IXP`（`Internet exchange point`）是用于连接不同ISP网络的基础设施。  
- `RIPE`是欧洲地区的互联网注册机构，它是全球五个地区级级互联网注册机构。互联网的`IP`地址分配是分级的，`ICANN`为全球IP地址进行编号分配，它会将部分`IP`地址分配给五个地区级的`RIP`（`APNIC`负责亚太地区）。其下为国家级注册机构，例如我国的`CNNIC`就是从`APNIC`获得`IP`地址的。  
- `MaxMind geolocation service`是一个通过`IP`地址获得**`地理地址`**的服务。国内可以考虑`qqzeng-ip`数据库，每月更新。包含了`大洲-国家-省份-城市-县区-运营商-行政区域代码-国家英文名称-国家代码-经度-纬度`  
``` Sql
SELECT city 
FROM ip 
WHERE INET_ATON('219.232.57.199') 
BETWEEN StartIpNum AND EndIpNum 
LIMIT 1
```
- `Overlay Network`是应用层网络，`peer-to-peer`与`client/server`都属于`Overlay Network`。中文参考文献：`《应用层组播研究综述》`和`《网络体系结构的研究现状和发展动向》`。  
- `Anycast`技术是一种网络寻址和路由方法。通过使用`Anycast`, 一组提供特定服务的主机可以使用相同的`IP`地址,同时,服务访问方的请求报文将会`IP`网络路由到这一组目标中拓扑结构最近的一台主机上。`Anycast`通常是通过在不同的节点处同时使用`BGP协议`向外声明同样的`目的IP地址`的方式实现的。  
- `BGP`-`Border Gateway Protocol`：边际网关协议。  
- `RIPE Atlas Probe`：是`RIPE`提供的一种服务，可以使用它位于全球的探针来测量网络的连接状况和可达状况。  
- **==小结==**
- 由于很多国家会对网络流量进行监视，所以越来越多的国家和个人希望能够在访问目的站点时，数据访问的路径能够避开某些特定的国家（例如`Unite States`）。巴西在这个方面做了卓越的工作，建立了直达葡萄牙的`IXP`以避免国内流量经过美国。  
- 因此本文对数据传输路径是否能够绕开特定国家进行了测量，他们利用了`RIPE Atlas Probe`和`MaxMind`的地理位置服务，进行了实验。  
- 本文对`Alex`的`Top100`站点进行了实验，实验过程中首先利用位于`Probe`的节点分别使用`ICMP`和`TCP`进行了`TraceRoute`。然后，利用`MaxMind`的服务获得路由路径中经由的国家。  
- 在此基础上，本文对位于`巴西`、`荷兰`、`印度`、`肯尼亚`和`美国`的节点进行了测量并得出了结论。  

----
论文下载地址：[\[Goto SigComm16\]](http://dl.acm.org/citation.cfm?doid=2934872)
