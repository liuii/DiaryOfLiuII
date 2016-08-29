# SigComm16论文阅读手记   

#### `G12P01` `2016-08-29` A First Look into Transnational Routing Detours  
- `IXP`（`Internet exchange point`）是用于连接不同ISP网络的基础设施。  
- `RIPE`是欧洲地区的互联网注册机构，它是全球五个地区级级互联网注册机构。互联网的`IP`地址分配是分级的，`ICANN`为全球IP地址进行编号分配，它会将部分`IP`地址分配给五个地区级的`RIP`（`APNIC`负责亚太地区）。其下为国家级注册机构，例如我国的`CNNIC`就是从`APNIC`获得`IP`地址的。  
- `MaxMind geolocation service`是一个通过`IP`地址获得**`地理地址`**的服务。国内可以考虑`qqzeng-ip`数据库，每月更新。包含了`大洲-国家-省份-城市-县区-运营商-行政区域代码-国家英文名称-国家代码-经度-纬度`  
``` SQL
SELECT city 
FROM ip 
WHERE INET_ATON('219.232.57.199') 
BETWEEN StartIpNum AND EndIpNum 
LIMIT 1
```
- `Overlay Network`是应用层网络，`peer-to-peer`与`client/server`都属于`Overlay Network`  
- `Anycast`技术是一种网络寻址和路由方法。通过使用`Anycast`, 一组提供特定服务的主机可以使用相同的`IP`地址,同时,服务访问方的请求报文将会`IP`网络路由到这一组目标中拓扑结构最近的一台主机上。`Anycast`通常是通过在不同的节点处同时使用`BGP协议`向外声明同样的`目的IP地址`的方式实现的。  
- `BGP`-`Border Gateway Protocol`：边际网关协议。  
- `RIPE Atlas Probe`：是`RIPE`提供的一种服务，可以使用它位于全球的探针来测量网络的连接状况和可达状况。  
- **小结**
- 由于很多国家会对网络流量进行监视，所以越来越多的国家和个人希望能够在访问目的站点时，数据访问的路径能够避开某些特定的国家（例如`Unite States`）。巴西在这个方面做了卓越的工作，建立了直达葡萄牙的`IXP`以避免国内流量经过美国。  
- 因此本文对数据传输路径是否能够绕开特定国家进行了测量，他们利用了`RIPE Atlas Probe`和`MaxMind`的地理位置服务，进行了实验。  
- 本文对`Alex`的`Top100`站点进行了实验，实验过程中首先利用位于`Probe`的节点分别使用`ICMP`和`TCP`进行了`TraceRoute`。然后，利用`MaxMind`的服务获得路由路径中经由的国家。  
- 在此基础上，本文对位于`巴西`、`荷兰`、`印度`、`肯尼亚`和`美国`的节点进行了测量并得出了结论。  



----
论文下载地址：[\[Goto SigComm16\]](http://dl.acm.org/citation.cfm?doid=2934872)
