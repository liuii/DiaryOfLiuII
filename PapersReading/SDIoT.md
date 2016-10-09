# SDIoT相关论文阅读手记  

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