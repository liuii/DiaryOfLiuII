# SigComm15论文阅读手记   
假期阅读了几十篇SigComm15的论文，选取其中比较有趣的几篇作为总结。

#### `G03P02` Analysis of Game Bot’s Behavioral Characteristics in Social Interaction Networks of MMORPG  
- 这篇文章主要讨论如何鉴别`永恒之塔`游戏中的机器人玩家。
- 这篇文章的最主要的特点是：将整个游戏的社交活动分为六个独立的网络，分别为`1好友`、`2私信`、`3帮派`、`4交易`、`5邮件`和`6商店`。另外将玩家活动分为五种情况：`Bot-All`、`Bot-Bot`、`Human-All`、`All-All`。通过研究这五种关系在六个网络中的行为模式来进行分类。
- 文章中的统计工具主要用到了`Jaccord Coefficient`和`Pearson Coefficient`。
- **思考与假设**：*把一个大的用户网络分为多个分离的子网络，然后对用户进行分析是一个很有趣的思路。*

#### `G03P03` BitMiner: Bits Mining in Internet Traffic Classification
- 该文章利用关联规则挖掘去寻找特定应用的数据流的特征。之前有过文章把应用程序的数据流按照字节和位置进行编码，然后进行关联规则挖掘。这样就可以通过监视网络数据流从而判断该数据流属于哪一个应用程序，这个工作对`网络管理`和`网络安全`都有益处。
- 这篇文章更进一步采用了对`Bit`与它的`Packet-order`和`Bit-order`进行编码形成一个`Item`，然后把数据流的前256个`Packet`转换为一个`TID`，然后利用改进的`FP-Tree`进行关联规则的挖掘。
- **思考与假设**：*加密数据流是否有可能存在某种与`Bit`相关的规则？如果存在，将是一个有趣的研究。*

#### `G03P06` Coracle: Evaluating Consensus at the Internet Edge
- 本文面对的是`Internet Edge`中的`一致性`问题。`一致性`问题原本是并行运算中的重要课题，但如今在云计算中更为重要。
- 本文是对`Consensus`算法的改进。有很多投票类型的算法：`Paxos`、`Raft`。
- `Raft`算法的一个动态展示：[Understandable Distributed Consensus](http://thesecretlivesofdata.com/raft/)
- **思考与假设**：*一个有趣的领域，但是没有想好我们可以在这个领域做些什么，并能够从中获得一些什么。只是本能的觉得这个领域很有趣。*

#### `G03P07` Could End System Caching and Cooperation Replace In-Network Caching in CCN?
- `CCN`是`Content-Centric Netword`的简写，是一种基于内容的网络。传统的网络是`Connection-Oriented`，但是在云计算的时代，面向连接的网络对内容的分布适应程度较差。所以`CNC`对`TCP/IP`、`IoT`和`WSN`都有较强适应性。
- 这篇文章讨论了云计算中资源的Cache策略，按照传统的观点在云计算平台中，为了提高内容的访问速度通常会在`CNC`网络中布置若干`Cache`，但是这种策略的开销太大。而云平台中的端点通常利用率不高，所以本文提出了用`Cloud-End`承担`Cache`工作的理念。
- **思考与假设**：*这种新的理念带来的是`路由算法`的改进，而针对`CNC`的改进则是目前的一个热点问题。*

#### `G03P08` Extreme Web Caching for Faster Web Browsing
- 通常在一个`边缘网络`中，会布置一个`Web Cache`，其目的是降低出口的流量，提高网络内用户访问Web的速度。通常`Cache`的Web对象的有效期是由Web服务端提供的，但是实际上大部分服务方提供的`有效期`都不是非常准确。这就导致`Cache`服务经常会失效。
- 本文对`Alex`前20名的网站进行监控，去统计对象的改变周期，从而对对象的`有效期`进行更为精确的预测。
- 所以`边缘网络`的`Edge Cache`要从本文构建的`Cloud Controller`来查询Web对象的`有效期`，从而提高`Cache`的性能。
- **思考与假设**：*这是一种监视Web对象失效的哨兵，在这个领域有很多可做的工作，使用机器学习来进行更为精准的`Online ML`会是一个研究方向。*  

#### `G03P09` FlyCast: Free-Space Optics
Accelerating Multicast Communications in Physical Layer
- 这篇文章利用了`Free-Space`光通信技术（类似于`LiFi`），其目的是在数据中的机架顶端布置`Free-Space`光通信的发送端和接收端，同时在天花板上布置`Ceiling Mirror`。同时每个机架上方包含一个液晶控制的分离器。
- 利用这种方式来进行`MultiCast`，提高`MultiCast`的性能。
- **思考与假设**：*带有空间位置的拓扑变换，具有很大的优化空间，应该可以提出更多的解决方案。*  
#### `G03P10` i-tee: A fully automated Cyber Defense Competition for Students
- 这是一个用于`网络安全`教学实验的开源平台。
- **思考与假设**：*未来如果开展`网络安全`的教学可以考虑采用这一平台，并在其之上进行进一步的开发。*

#### `G03P14` The Internet of Names: A DNS Big Dataset
- 文章针对`DNS`系统的安全进行了讨论，最主要的工作是利用`Hadoop`集群对海量的`Domain Names`进行了测量与分析。
- 这项工作对1.4亿的域名进行了跟踪分析，提供了数据集接口可以供进一步的研究。
- **思考与假设**：*利用`Hadoop Cluster`还可以对哪些我们忽视的内容进行监视呢？*

#### `G03P17` Yo-Yo Attack - Vulnerability in auto-scaling mechanism
- `DDOS`是利用大量的僵尸网络对目标发起连接，从而使目标失去服务能力。但是在拥有弹性的云计算平台的今天，`DDOS`攻击显得就有些力不从心了。
- 本文提出了一种新的`EDOS|经济阻绝攻击`方式。通过测量目标的`Response Time`来决断当前目标所使用的云资源数量。然后就可以采用间断攻击的方式来降低攻击的花费。
- **思考与假设**：*网站的`Response Time`可以看做是网站服务的一个重要指标，如果我们监控网站在一段时间内`Response Time`的变化，是否可以从中挖掘出一些有用的信息呢？*

#### `G03PZZ` Sub-Nanosecond Time of Flight on Commercial Wi-Fi Cards
- 本文提出了一种提高测量Wifi信号`Time-to-Flight`的新方法。
- 这种方法能够把误差降到10cm到25.6cm（12-15米的范围）。
- **思考与假设**：*相信这种新的测量方法对于我们现在做的室内WiFi定位会有很大的帮助。*

#### `G09P03` A Mininet-based Virtual Testbed for Distributed SDN Development
- `Mininet`是用来进行网络实验的开放平台，尤其是针对`SDN`方面的研究。
- **思考与假设**：*应该对`Mininet`了解一下，以进入`软件定义网络`研究领域。*

#### `G09P05` BitCuts: Towards Fast Packet Classification for Order- Independent Rules
- 本文提出了一种比特级别的`决策树`算法，该算法用于快速分组（`Packet`）分类。
- **思考与假设**：*感觉文章带来了很大的启迪，但是我抓不住到底他提醒了我什么？隐约感觉可以将这种方法移植到另外一个问题的解决方案上，但是我还想不到那个问题是什么。*

#### `G09P08` EPOXIDE: A Modular Prototype for SDN Troubleshooting
- 这篇文章针对`SDN`的`Troubleshooting`做出了基于`Mininet`的整合。
- 项目地址：[epoxide](http://github.com/nemethf/epoxide)
- **思考与假设**：*这种整合工具对今后`软件定义网络`的研究有很大的帮助。*

#### `G09P10` FreeSurf: Application-Centric Wireless Access with SDN
- 本文讨论的是如何利用`公共Wifi`为特定应用提供服务。
- 本文提出了一种新的身份验证机制，从而使`公共Wifi`的提供者对特定应用进行服务。
- **思考与假设**：*这是`公共WiFi`提供者盈利的一种方式，感觉可以和商业公司就此方面展开合作研究。*

#### `G09P12` NetFPGA - Rapid Prototyping of Networking Devices in Open Source
- NetFPGA是做网络实验的开源硬件。
- **思考与假设**：*德致伦有售：[Diailent Inc.](http://www.digilentinc.com)*

#### `G09P14` nf.io: A File System Abstraction for NFV Orchestration
- 可以用于搭建`NFV`平台，利用了`Linux File System`。
- 演示地址：[nf.io](http://faizulbari.github.io/nf.io/)
- **思考与假设**：*在做云机器人架构时，是否可以利用这个思路？*

----
论文下载地址：[\[Goto SigComm15\]](http://dl.acm.org/citation.cfm?id=2785956)
