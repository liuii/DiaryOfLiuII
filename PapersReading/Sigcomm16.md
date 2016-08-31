# SigComm16论文阅读手记   

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