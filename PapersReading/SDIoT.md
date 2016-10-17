# SDIoT相关论文阅读手记  

#### `P10` `2016-10-17` Refactoring Internet of Things middleware through Software-Defined Network  
- 现有的物联网设备存在着以下的挑战：  
  1. 互联性：大部分能够访问互联网的设备，使用一种专有的通信方式与制造商的服务器或其他节点进行通信。即使提供了APIs，数据也首先被送到专有服务器上。  
  2. 安全与隐私：通常设备收集的数据在发送的时候使用明文，同时没有和服务器之间的验证。  
  3. 管理：IoT设备通常采用睡眠调度机制来减少能量的消耗，因此要避免部署传统的网络管理解决工具（例如SNMP、NETCONF、ICMP-based等）。  
  4. 数据结构：`[12]`这篇文献指出，80%的健康数据是无结构的，因此需要在大容量的数据中进行挖掘，以找到相关信息，以便对未来的后果进行预测。  
- `REMOA`的主要组件包括：  
  1. Transparent Proxy：该组件用于分析所有输出到互联网的传输。它接收健康数据，送到相应的应用，并记录用于管理目的的MIB（Management Information Base）信息（关于things的操作）。必要时应用过滤和重定向规则到网络传输中。  
  2. SNMP Agent和Proxy Agent：负责为不支持SNMP的节点处理SNMP请求，他们利用透明代理模块存储在MIB中的数据来相应请求。  
  3. Gateway：负责在应用于节点之间进行透明的通讯，只有当节点允许被访问操作的时候。在必要的时候Gateway还要负责在不同的技术之间翻译信息。  
- 重构之后功能：  
  1. Transparent Proxy：它的任务由AP与提供服务的服务器来完成。AP基于流的规则转发分组，这些规则由ThingsFlow应用程序通过控制器发出。收集到数据通过IPSec隧道将数据发送到服务，然后进行parse和process。  
  2. Things management：由基于SNMP改为基于OpenFlow计数器，由ThingsFlow提取的计数器包含提取的时间戳，服务通过提出ThingsFlow中存储的时间戳来实现它的管理机制。  
  3. Gateway：由AP实现的分组转发功能。目前的趋势是使用基于IP的方法，例如6LowPAN。  

----
#### `P09` `2016-10-16` A Low Power Software-Defined-Radio Baseband Processor for the Internet of Things  
- 该文提出了一种基于SDR的处理器设计，以可编程的平台来代替专用集成电路的实现。  

----
#### `P08` `2016-10-14` Software-Defined Wireless Sensor Networks and Internet of Things Standardization Synergism  
- 有些工作提出将传感器网络的路由协议分为不同的类别：`data-centric`、`hierarchical`、`location-based`、`QoS`、`network-flow`、`data aggregation`。  
- 软件定义无线传感网络方法包括：`Flow-Sensor`、`Sensor OpenFlow`、`SDWN`、`TinySDN`。  
- 本文重点讨论`RFC 6550 - RPL: IPv6 Routing Protocol for Low-Power and Lossy Networks`和`TinySDN`。  
- TinySDN包括两个部分：`SDN-enabled sensor node`和`SDN controller node`。  
  - `SDN-enabled sensor node`包含三个主要的组件：  
    1. TinyOS应用程序：等价于终端设备，并依赖于运行于WSN节点上的应用。它用来生成将要传输的数据。  
    2. TinySdnP：接收分组并尝试与流表入口进行匹配，如果无法匹配则向`SDN controller node`发出请求。  
    3. ActiveMessageC：用来执行通信任务的TinyOS的组件，执行例如数据或控制分组的转发、拓扑信息的收集。  
  - `SDN controller node`包含两个模块：  
    1. Sensor mote module：运行于WSN设备，它的两个主要任务包括：使用`ActiveMessageC`与`SDN-enabled sensor nodes`建立通信；利用`SerialActiveMessageC`向`Controller server module`转发分组。
    2. Controller server module：包含控制层逻辑，例如：主机控制应用、网络流管理和拓扑信息。  
- `Routing Protocol for Low Power and Lossy Networks (RPL)`是IoT标准化的另外一个成果。  

----
#### `P07` `2016-10-13` Software-Defined Wireless Network Architectures for the Internet-of-Things  
- 物联网系统通常分为四个层次：  
  1. 传感器、无线传感网络、独立传感器、RFID等等。  
  2. 信息聚合层，用来收集传感器数据的层次，包括网关和用来收集数据的智能手机等等。  
  3. 数据处理节点层，用来处理收集上来的节点数据。  
  4. 因特网连接层，用于为更多的用户提供服务。  
- 除了在异构性和网络管理方面的考虑之外，在目前物联网中还存在着如何利用IoT产生的大数据的问题，以及如何在这样一个复杂精致的系统中保持细颗粒度的服务质量保证也是一个复杂的任务。  
- 本文的主要进行了三个方面的研究：  
  1. 管理传感层的SDN解决方案。  
  2. 通过移动网络和无线局域网的端到端资源管理的SDN架构。  
  3. 支持ICN（Information-Centric Networking）核心组件的SDN架构，以构建SaaS（Sensing-as-a-Service）。  
- SDN的概念与背景：  
  1. SDN控制器目前已经提出了：NOX，POX，Floodlight，Beacon和OpenDayLight。  
  2. SDN控制器将策略转换成详细的规则，然后通过SDN的协议发送给路由器和交换机。已经提出的协议包括：`Forward and Control Element Specification, ForCES`、`Interface to the Routing System, I2RS`和`OpenFlow`。  
- 部署SDN有以下的好处：  
  1. 提供了能够简单的最优化的中心网络管理器，它可以执行来自高速连接站点的命令。  
  2. 可以根据网络条件而简单的采用相应的网络协议，例如在拥有中心控制器后可以方便的管理负载均衡。也可以根据网络的负载采用不同的媒介访问方法，例如在负载增加的时候，可以把媒介访问控制方法由载波侦听改为分时的策略。  
  3. 由于通过简单的重新配置就可以改变交换设备的操作，因此SDN提供了与供应商无关的控制。这就意味着交换设备不在需要装备那些胜任所有可能情况的所有的协议，同时还允许管理异构的实体。  
  4. SDNs提供了强大的虚拟化工具，允许管理者最大程度的利用他们的设备。  
- 将SDN控制器部署在Sink Node或Aggregator Point的位置，位于中心的位置使得控制器更适合去优化拓扑控制。可以更好的控制传感器的`睡眠/激活`循环。对于拥有多个传感器的节点，可以控制每个传感器节点的工作循环。从而支持基于查询的通信方式，并节省能源。  
- 然而由于传感器节点不能支持SDN，因此需要对其进行改造。`[5]`提出了利用FPGA和MCUs构成的SDN控制传感节点的例子。是其并未基于OpenFlow，因此另外一篇文章提出了如何改造OpenFlow去适应物联网设备的需求。  
- 传感器及服务（SaaS）的概念对于IoT的商业化非常重要，但是传统的基于关键词的搜索引擎对物联网设备并不适用。此外对服务的订购也有很大的差别（包括频率、范围等等）。Semantic Sensor Network项目使用本体来设计一个串联的传感器数据库。  
- 目前，由于`本体`所具有的推论能力和添加语义的能力，使得本体在IoT领域的关注度正在增长。例如`[1]`为SSN扩展了内容感知，以改进传感器搜索技术的精确度。 
- 要实现SaaS存在着两个关键的挑战：  
  1. 如何将应用需求翻译为传感服务。  
  2. 如何正确的分发内容给用户。  
- 而解决这两个问题的答案就是`information-centric networking, ICN`，因为用户更关注于感兴趣的数据，而不关心数据由何处产生。ICN分为两层，控制层用于匹配用户的兴趣与实际内容，另一层用来进行数据转发。而SDN很明显适合解决上面的两个问题：SDN控制器同时拥有南桥和北桥的接口，所以非常适合将用户的需求转为对网络资源的控制，然后通过产生的规则来完成第二个挑战。  
- 在ICN中，需要将数据分组进行修改，将名字（标签）附加在IP地址的后面，例如：`Data Oriented Network Architecture (DONA)`、`Network of Information (NetInf)`和`Publish/Subscribe Internet Technologies (PURSUIT)`架构都提出了命名方案。命名方案的形式为：`P:L`，其中P为拥有者公钥的密码学哈希函数，L是内容提供者选择的一个全球唯一的标签。而`Named Data Networking, NDN`架构提出了一个层级的命名方案，用对人更为友好的语言来表述。  
- 但是这些命名方案没有任何可以使用链接数据的语义，`[16]`提出了一种基于空间索引的命名方案，用于基于SDN的订阅/发布系统。它使用了固定长度比特串，来表示细颗粒度的数据请求（例如：针对特定区间以及特定地区的温度）。
- 此外，对于ICN，QoS和一致性也是两个重要的话题。  

----
#### `P06` `2016-10-12` Pre-emptive Flow Installation for Internet of Things Devices within Software Defined Networks  
- 在软件定义网络中，当一个交换机收到一个分组之后，如果在流表中没有相应的规则，则会向中央控制器请求相应的规则。但是交换机通常是内存受限的，所以一个稀有的分组规则很快就会过时被释放。  
- 而物联网设备的数据具有周期性的特点，很有可能会被任务繁重的交换机反复的释放它对应的处理规则。  
- 因此就需要动态的学习周期性间隔传输的网络流，并在分组到达之前将规则安装到交换机中。  
- 自物联网产生的数据有两种可能：一种是周期性的小流量数据，另外一种是因为突发情况导致的激增流量。这两种情况都需要网络来进行处理。  
- 本文分别介绍了IoT的数据特点和SDN的`标准`、`控制器`和`交换设备`。  
- 常见的SDN控制器包括：`POX`、`NOX`、`Beacon`、`Floodlight`、`Ryu`。  
- `NFV` <-- Northbound Interface --> `SDN Controller` <-- Southbound Interface (OpenFlow) --> `Switch Devices`  
- OpenDayLight提供了两种北桥接口：OSGi和REST，前者用于本地应用的执行，后者用于远程web调用。  
- SDN交换机可由三种方式获得：  
  1. 传统的交换机可以通过升级固件来支持OpenFlow。  
  2. 裸机可以选择安装PicOS（支持OpenFlow）和Cumulus Linux。  
  3. 利用虚拟交换机，例如：OpenVSwitch和OFSoftSwitch。  
- 本文的算法分为三个步骤：  
（1）记录流的时间记录：  
``` pascal
On packet in;
flow = SourceIPAddress:SourcePortNumber:SwitchID;
if flow is new then
  create flow record;
  record packet arrival time;
else
  flow record exists;
  append packet arrival time to flow record;
end
```
（2）统计每个流的标准差，以决定是否是一个周期性信号：  
``` pascal
for each record in flow records do
  if number of times observed > minimum number of samples then
    Calculate mean of times;
    Calculate standard deviation of times;
    if standard deviation < threshold then
      Store flow installation timing requirements;
    end
  end
end
```
（3）提前将规则安装到交换机：  
``` pascal
On timer expiration;
for each record in flow records do
  if (current time + pre-empt value) - previous flow installation time >= flow mean then
    Send flow installation message to switch;
  end
end
```
----
#### `P05` `2016-10-10` Software-Defined Wireless NetworkingOpportunities and Challenges for Internet-of-Things: A Review  
- 整合SDN与IoT有以下好处：
  1. SDN拥有潜在的智能路由传输，并使用未能充分使用的网络资源。  
  2. 将SDN整合到IoT当中，可以简化信息获取、信息分析、决策制定和行为实现处理。  
  3. 可以提供网络资源的可见度；基于用户、组、设备和应用的访问管理，并最终提供在用户与设备之间交换数据的能力。  
  4. 研究者在SDN中设计的智能算法可以构建高效的传输模型分析，可以简化IoT设备的数据收集工具。也便于设计新奇的调试工具。
  5. 采用SDWN（软件定义无线网络）可以令IoT网络拥有基于需求的弹性和敏捷性。  
- SDN的早期工作：  
  1. OpenFlow和ONF。  
  2. 详细介绍实验平台和调试工具：MiniNet、W3、FatTire和fs-sdn。  
- IoT中的SDN机会：  
  1. 无线网络中的SDN。  
  2. 支持SDN的混合架构。  
- 现存的研究挑战：  
  1. SDN与IoT网络的安全性。  
  2. SDN与IoT的可扩展性。  
  3. 深度包检测。  
  4. 边际AP的丢包。  

----
#### `P04` `2016-10-09` SOFTWARE-DEFINED INTERNET OF THINGS FOR SMART URBAN SENSING  
- 文中提出了现有的物联网开发方式的几种缺陷：  
  1. 高昂的成本和维护成本。  
  2. 对于应用改变的灵活性差。  
  3. 无法高效率的利用资源。  
  4. 开发和部署的周期过长。  
- 文中提出了物联网开发的几个趋势：  
  1. 共享物理基础设施。  
  2. 新兴的软件定义架构。  
  3. 应用程序接口的流行。  
- 本文提出了一种SDIoT架构：  
  1. 物理层：包括无线传感网络设备、网络传输设备和云计算设备。  
  2. 控制层：针对前面提出的三种类型设备的控制器。  
  3. 应用层：提供APIs。  
- 本文详细的从以下几个方面讨论了该架构：  
  1. 传感器平台与数据获取服务。  
  2. 网络与数据传输服务。  
  3. 云数据中心与数据处理服务。  
- 本文提出了SDIoT存在的问题和有潜力的解决方案：  
  1. 南桥接口设计：采取组合抽象策略和中间件的方法，基于有限状态机的数据处理和传输的抽象，另外将中间件迁移到控制器以达到节省能耗的目的。  
  2. 控制层设计：中央逻辑控制器需要完成三个目标，高可扩展性、高性能和高鲁棒性。因此部署多个控制器是解决这一问题的最佳方法。  
  3. 移动管理：通过协调器来完成物理传感器移动时的状态迁移。  
  4. 冲突处理：通过改进传感器平台来完成这一工作。  
  5. 允许QoS的传输调度。  
  6. 数据中心中的资源映射。  

----
#### `P03` `2016-10-05` A System Architecture for Software-Defined Industrial Internet of Things  
- `mobile ad hoc network, MANET`移动自组织网络。  
- `Multinetwork INformation Architecture, MINA`  
- `Network Calculus`网络微分学。  
- 与数据中心网络中的SDN不同，应用于物联网中的SDN有以下的区别：  
  1. 数据中心的SDN利用快速的互联通道收集诸如带宽消耗等统计信息，而物联网通常要收集异构且松散的网络数据，其地理分布通常也比数据中心要大。  
  2. 物联网不仅关注数据中心SDN所关注的链路利用率和吞吐量，它更关心异构网络和时间相关特性，例如延迟、抖动、分组丢失和吞吐量。  
  3. 由于物联网具有异构性，因此单一目标的优化算法将不起作用，数据中心网络调度中常用的`装箱方法`、`模拟退火`等优化算法无法直接用于物联网环境。  
  4. 当前SDN的一些实现（例如OpenFlow），聚焦于控制器与底层设备之间的交互（称为`南桥`）。而对于应用服务层与控制器之间的`北桥`则没有足够的标准，目前的一些服务描述仍依赖于底层设备的参数。  
- 本文使用了本体和规则等语义技术进行资源匹配，利用本体来描述以下信息：  
  1. 网络、设备资源以及服务的特点和能力。  
  2. 使用层级式方法来描述IoT任务，一个高层级任务可以由一系列低层级任务来替代。  
``` pascal
LocateAndTrack(Vehicle,Location)= FindLocationResources(
	in:Location,
	neededCap:LocationCapability,
	out:res:SetOf(LocationResources));
Match(
	in:Vehicle, 
	SetOf(LocationResources),
	neededCap:Tracking,
	out:SetOf(LocationAndMatchingResources))
For all Res in SetOf(LocationAndTrackingResources) Do
  If Res = Camera then
     TrackUsingVisualMeans(
     	in:Res,
     	neededCap:CameraTracking,
     	out:TrackingData)
  ElseIf Res = MeshRouter then
     TrackUsingDigitalMeans(
     	in:Res,
     	neededCap:DigitalTracking,
     	out:TrackingData)
```
- **本文使用了遗传算法来优化QoS**  

----
#### `P02` `2016-10-03` A System Architecture for Software-Defined Industrial Internet of Things  
- `Industrial Internet of Things, IIoT`工业物联网。  
- `WirelessHART`是一种基于`Highway Addressable Remote Transducer, HART`协议的无线传感网络。  
- `WebSocket`是一种计算机通信协议，通过一个单独的TCP连接提供一个全双工的通信信道。参见RFC 6455。此外`WebSocket`在`Web Interface Description Language, IDL`中的API已经由`W3C`进行了标准化。  
- `Constrained Application Protocol, CoAP`是一种用于非常简单的电气设备中的软件协议，允许这些设备利用互联网来进行交互。  
- `Common Object Request Broker Architecture, CORBA`是由`Object Management Group, OMG`定义的标准，旨在促进不同平台系统之间的通讯。CORBA可以使具有不同操作系统、编程语言和计算机硬件的系统具有相互协作的能力。它是一种分布式对象的泛型。  
- `IPv6 over Low power Wireless Personal Area Networks, 6LoWPAN`  

----
#### `P01` `2016-09-30` SDIoT: a software defined based internet of things framework  
- 将`SDNetwork`、`SDStorage`、`SDSecurity`和其他的`SDSystem`进行整合。  
- **本文可以作为重要的参考文献**。  