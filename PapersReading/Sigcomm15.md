# SigComm15论文阅读手记   
假期阅读了几十篇SigComm15的论文，选取其中比较有趣的几篇作为总结。

#### `G03P02` Analysis of Game Bot’s Behavioral Characteristics in Social Interaction Networks of MMORPG  
- 这篇文章主要讨论如何鉴别`永恒之塔`游戏中的机器人玩家。
- 这篇文章的最主要的特点是：将整个游戏的社交活动分为六个独立的网络，分别为`1好友`、`2私信`、`3帮派`、`4交易`、`5邮件`和`6商店`。另外将玩家活动分为五种情况：`Bot-All`、`Bot-Bot`、`Human-All`、`All-All`。通过研究这五种关系在六个网络中的行为模式来进行分类。
- 文章中的统计工具主要用到了`Jaccord Coefficient`和`Pearson Coefficient`。
- **思考与假设**：*把一个大的用户网络分为多个分离的子网络，然后对用户进行分析是一个很有趣的思路*。

#### `G03P03` BitMiner: Bits Mining in Internet Traffic Classification
- 该文章利用关联规则挖掘去寻找特定应用的数据流的特征。之前有过文章把应用程序的数据流按照字节和位置进行编码，然后进行关联规则挖掘。这样就可以通过监视网络数据流从而判断该数据流属于哪一个应用程序，这个工作对`网络管理`和`网络安全`都有益处。
- 这篇文章更进一步采用了对`Bit`与它的`Packet-order`和`Bit-order`进行编码形成一个`Item`，然后把数据流的前256个`Packet`转换为一个`TID`，然后利用改进的`FP-Tree`进行关联规则的挖掘。
- **思考与假设**：加密数据流是否有可能存在某种与`Bit`相关的规则？如果存在，将是一个有趣的研究。

#### `G03P06` Coracle: Evaluating Consensus at the Internet Edge
- 本文面对的是`Internet Edge`中的`一致性`问题。`一致性`问题原本是并行运算中的重要课题，但如今在云计算中更为重要。
- 本文是对`Consensus`算法的改进。有很多投票类型的算法：`Paxos`、`Raft`。
- `Raft`算法的一个动态展示：[Understandable Distributed Consensus](http://thesecretlivesofdata.com/raft/)
- **思考与假设**：一个有趣的领域，但是没有想好我们可以在这个领域做些什么，并能够从中获得一些什么。只是本能的觉得这个领域很有趣。
