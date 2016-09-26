# BackScatter相关论文阅读手记  

#### `SigComm16G08P01` `2016-09-23 to 2016-09-26` Enabling Practical Backscatter Communication for On-body Sensors  
**`0. ABSTRACT`**  
- `Frequency-shifted backscatter, FS-Backscatter`基于这样一种新奇的思想，backscatter标签将载波信号移动到一个相邻无重叠的频率波段上。同时将Backscatter信号从主要信号的频谱中独立出来，以拥有更好的编码鲁棒性。  
- 本文展示了利用商业WiFi和Bluetooth作为载波发生器和接收器，使得Backscatter的通讯的距离达到了4.8米。并且可以支持分组级别和比特级别编码方法的一系列码率。  
- 同时本文展示了利用多个用于移动可穿戴设备的典型无线电波，来构建多路载波与多路接收的场景，以改进鲁棒性。最后展示了频移20MHz仅使用几十微瓦（`micro-watts`）的低功耗标签。  

**`1. INTRODUCTION`**  
- `Bluetooth Low Energy, BLE`蓝牙低功耗技术是目前广泛使用的低功耗通信技术。很多可穿戴或体表传感器能源受限，因此目前大部分的设备都在使用BLE技术。但是BLE在传输数据的活动模式中的功耗达到了毫瓦（`milli-watts`）。而Backscatter标签进行端到端的通信时需要消耗几十微瓦。因此使微功耗体表传感器、微型植入传感器、灵活的瘦可穿戴设备称为可能。  
- 当需要为体表传感器加入BackScatter时，与可以部署可信的支持Backscatter的AP与Reader不同，本文需要处理的是一个移动的环境。可能需要对智能手机与可穿戴设备的电波芯片组进行改造以增加对Backscatter的支持。但是实际情况非常不理想，还会加剧因为backscatter反向链路损失和backscatter天线反射损失造成的不可信的编码场景。  
- `WiFi Backscatter`技术中，WiFi接收设备查询每个分组的`Received Signal Strength Indicator, RSSI`或`Channel State Information, CSI`的数值，首先平滑这些数值以移除WiFi信号的自然变化，然后使用平均信号中的信号强度变化来提取低速率的Backscatter信号。这种方法在物联网环境中需要一个静态的Backscatter标签并拥有一个巨大的天线。但是在移动场景中，这种方法很难奏效，由于移动标签只能包含一个小型的天线，另外移动和身体阻塞会造成WiFi信号持续的变化。这会造成非常低的信噪比（`Signal Noise Ratio, SNR`），并由于距离和吞吐量而持续的降低传输性能。  
- 本文的核心思路是一种简单并且高效的技巧：如果一个backscatter标签可以把入射WiFi或Bluetooth的载波移动到一个干净的WiFi或Bluetooth波段，然后接受者就可以在频移后的波段看到一个干净、没有载波干涉的backscatter信号。标签可以频移后的波段执行OOK（`on-off keying`, 可以产生近似于方波的波形）来传送信息。例如，智能手机可以作为Bluetooth的载体，一个体表传感器可以作为标签将信号调制并频移20MHz，然后一个例如腕带之类的Bluetooth接收器在相邻的波段接收频移信号。  
- 本文设计了一个低功耗的基于环路振荡器的时钟发生器，用于微瓦级功率并具有温度诱导频率变化的FS-Backscatter标签。本文的FS-Backscatter有以下贡献：  
  1. 设计、实现并评估了一个实际的用于体表设备的Backscatter系统，该系统具有通讯的超低功耗并兼容目前的商业WiFi与Bluetooth信号。本文展示了一个最远操作4.8m，并根据发射器与接收器的配置具有几十bps到几十Kbps的FS-Backscatter。
  2. 本文展示了利用多个可移动设备的过剩无线电波，并将多个发送器或接收器进行组合，从而增强系统性能。本文展示了能够增强25%到100%，本文利用两个发送器和接收器将吞吐率提升到48.7kbps。  
  3. 本文通过使用一个基于`ring-oscillator`（将奇数个非门链接起来，最后一个非门的输出作为第一个非门的输入）的时钟，使得FS-Backscatter标签的操作能耗到达了45*u*W。并且对由于环境变化产生的频率变动具有鲁棒性。  

**`2. CASE FOR FS-BACKSCATTER`**  
- **`2.1 Infrastructure-assisted Backscatter`**  
- 几种现存的技术都依赖于载波生成器、Backscatter信号编码器等基础设施，当然所有的RFID reader都采用这种方式。它们产生一个窄带载波然后执行`self-interference cancelation`来将Backscatter信号从载波中分离出来。但是由于RFID reader基础设施并不常见，所以最近的一些方法设计了一些将reader功能嵌入到现有设备的革新方法。  
  1. `BackFi`改造了一个WiFi AP，增强了AP取消OFDM（Orthogonal frequency-division multiplexing，正交频分多路复用技术指将信息编码至不同的频段以提高传输性能）载波信号。这一方法带来的好处就是简化了标签。一个简单的`ASK发送器`标签可以简单的Backscatter由AP生成的WiFi信号，而不用担忧OFDM信号结构的复杂度。  
  2. `BLE-Backscatter`提出了一种基础设施使backscatter标签能够直接和现有的BLE接收器进行通信。这个基础设施组件是一个简单的CW（Continuous Wave，振幅与频率恒定的波）发射器，Backscatter标签调制CW的Tone来仿真一个BLE发射器，因此可以使用通用的BLE接收器来接收调制后的信号。因此BLE-Backscatter标签因为不再需要生成载波而节省了能耗，但是因为它仿真了BLE栈，因此比`ASK-Modulating`Backscatter标签更为复杂、功耗更高。  
  3. `Passive WiFi`使用了和`BLE-Backscatter`类似的基础设施使标签与常用802.11b WiFi设备之间进行backscatter通讯。`Passive WiFi`包含一个载波发射器，用来发射一个单音信号。`Passive WiFi`标签在反射这个信号的时候生成并调制出802.11b的基带信号。尽管这种技术不需要改动现有的设备，但是其标签需要生成完整的802.11b基带信号，因此比`ASK-Modulating标签`更为复杂、能耗也更高。  
- 上述方法因为需要使用辅助基础设施并且需要额外的硬件，因此无法用于移动场景。  
- **`2.2 Infrastructure-less Backscatter`**  
- 第二类的方法利用了现有的一些载波（例如TV或WiFi载波），backscatter这个信号并被一个通用的接收器接收。由于TV载波存在衰减、质量不一的特点，所以这种载波不适用于需要连续监控的移动场景。  
- 但是`WiFi Backscatter`可以利用一个通用的WiFi来作为发送器和接收器，可以使用手机作为发射器、利用智能手表作为接收器。标签端使用基于ASK的backscatter。  
- 在信号处理方面，关键挑战就是在没有自干扰抵消技术的支撑下将backscatter信息从载波中分离出来。因此如果被接收的信号在一个足够长的窗口上进行平均，就可以将backscatter调制的信号分离出来。在模拟领域可以利用`Envelope Detector`，或者在数字领域使用低通滤波，来
测量一个backscatter信号是如何改变入射信号的传输特征。  
- 将静态物联网设置环境中的WiFi Backscatter用于可穿戴场景还有若干挑战：：  
  1. 由于主激励器比backscatter的强大很多，只在非常小的范围内才能达到均值、降低信噪比的要求。  
  2. 由于人的移动造成的短暂变化，以及频道对移动环境的相应，需要动态跟踪信噪阈值，反过来需要编码对被选中的阈值敏感。  
- 本文的实验使用`a standard 3dBi omni-directional antenna`没能发现RSSI的变化。而使用`9dBi directional antenna`在0.2米的距离时可以达到19bps。WiFi Backscatter的低性能的原因可能包括：  
  1. `低信噪比`：第一个关键因素就是因为无处不在的载波的强大干扰，限制了这种方案的有效范围和数据速率。在0.1m的距离时SINR（`Signal to Interference and Noise Ratio`）是-47dB，而距离达到2m时SINR仅有-71dB，考虑到dB是10log<sub>10</sub>Ratio，因此要达到可用的数据速率则有效范围只有几厘米，如果距离到达1m则数据速率就减到几个bps。  
  2. `移动引入的变化`：第二个问题就是移动会改变入射信号的传播特征，这就使得编码对选择的阈值高度敏感。本文采用1m的距离，固定情况下其信号强度是-35dBm（x *dBm* = 10log<sub>10</sub>`P/1mW`，P是信号能量）。然而如果接收器进行移动，信号会在-80dBm到-20dBm之间进行变化。  
- **`2.3 FS-Backscatter: Key Ideas and Challenges`**  
- `FS-Backscatter`的核心思想就是在反射入射波时，将其进行频移并调制20MHz，这样就可以将信号调制到一个与原始信号无覆盖的区域内。这样接收器就可以在一个较为干净的波段来接收backscatter信号。这样做的可行性有两个原因：  
  1. 文章对WiFi信号和BLE信号都进行了实验，发现移动后的信号从主载波中被清晰的分离出来。  
  2. 第二个原因就是现代的WiFi和Bluetooth接收器被设计为对有结构的微弱信号极度敏感，例如分组的序文。例如CC2560/CC2564蓝牙接收器可以对-95dBm的分组进行检测，在几十米的范围内只需要消耗几十毫瓦。可以利用这种敏感性来对抗由于反射（30dB）和反向链路引起的信号损失。而且On-body传感器只需要几米的接收距离，远远低于Bluetooth和WiFi接收器的工作范围，因此就有了额外的信号损失空间来弥补因为身体天线造成的信号损失。  
- 频移带来了很多的可能性，但是也面对着实际当中的问题与挑战：  
  1. 在现实中是否可用？如果可用，性能如何？什么情况下可以工作，什么情况下会失败？现有的常用无线电设备是否公开了允许这样做的接口（APIs）？  
  2. 功耗问题。因为无覆盖的WiFi波段之间有20MHz的差距，因此标签需要一个20MHz的振荡器。这实际上比ASK调制的几十到几百kbps要高很多，而更高的时钟频率意味着更多的能量消耗。在标签上损失了多少能耗？是否有办法缓和能耗的损失，并保持其在几十微瓦？
- 本文后面的内容，主要描述上面两个问题。  

**`3. FREQUENCY-SHIFTED BACKSCATTER`**  
- **`3.1 FS-Backscatter on Commodity Radios`**  
- 如果使用通用的WiFi或BLE芯片组在广播模式下，将载波频移到相邻的频率波段上并在这个波段上进行调制，接收器能够在这个相邻的波段上解码backscatter信号？  
- 对于分组级别的`FS-Backscatter`（每个分组产生一片信息RSSI）：WiFi（CC3200）实验表明最远可操作距离为4.8m，平均波特率为313.8bps。BLE（CC2650）实验表明操作距离为4.4m，平均数据速率为45.8bps。  
- 由于通常的芯片只能获得每个分组的平均RSSI值，所以backscatter标签的数据速率是和分组相关，因此为了提高传输速率就需要能够从更细的颗粒度上获得RSSI的数值。本文采用了TI的公开了底层接口的BLE芯片，该芯片提供了一个选项，可以绕过BLE栈直接获得更细颗粒度的RSSI值。  
- 本文在比特级别的实验中使用了CC2541，以100MHz的频率对RSSI进行采样（获得的是10us期间的频道平均值），然后对信号进行解码。最大有效距离为3.6m，数据传输速率为50kbps。尽管不能利用分组调制中结构性带来的接收器高敏感，但是因为移动到了一个干净的频段，因此仍然要比没有频移的ACK方法更好。  
- 频移backscatter需要一个相邻的波段（更低或更高）来传输，如果相邻的波段没有空闲将会导致该方法无法使用。但是值得注意的是有一些WiFi频道很可能不会用于活动传输。2.4GHz的WiFi有14个频道，实际上只有11个可以使用，因为12和13有着严格的发射限值需求，以防止干扰相邻的被限制使用的频道。
- 然而Backscatter信号十分的微弱，远低于发射限制，因此可以从`Channel 9`发射载波，从`Channel 13`监听。实验表明，从频道9发射，在频道13接收的信号只有-85dBm（由FS-Backscatter标签天线测量）。比WiFi载波信号低了30dB，接近于噪声级别。因此FS-Backscatter不会干扰到13频道周围的无线电波。  
- 由于FS-Backscatter标签仅包含一个RF晶体管和天线，因此理论上可以频移任何的信号。因此如果有多个发送器和接收器工作于不同的频段，那么就可以得到更好的性能和鲁棒性。  
- **`3.2 Low-power FS-Backscatter Tag`**  
- 在FS-Backscatter标签的设计中，由于标签需要先对载波进行频移20MHz然后再进行调制，因此是否能够将标签的操作功耗限制在微瓦级别是需要回答的主要问题。  
- 从直觉来说，对载波的频移越大功耗就越高，下面从标签的三个组成部分来讨论频移带来的最大功率消耗：  
  1. `RF Transistor`：RF晶体管是一个MOSFET（金属氧化物半导体场效应晶体管）包含一个大约2.1pF的电容（ADG902）。它的能量消耗可以利用公式：0.5CV<sup>2</sup>F，其中C为晶体管的电容，V是门电压，F是操作晶体管的频率。即使以20MHz的频率来操作晶体管，其功耗也只有21uW。因此RF晶体管的功耗很低，并与频率呈线性关系。  
  2. `Transmission Logic`：本文设计了一个数字电路来实现发送逻辑，并触发RF晶体管。使用了一个大约1.5pF的电容的与非门来打开或关闭晶体管，因此该模块的功耗大约为15uW。  
  3. `Clock generator`：通常使用振荡器来生成时钟，一旦开始频移几MHz，能耗也相应的升高几个毫瓦。  
- 因此振荡器是是功耗中，所耗费资源最大的部分。因此本文目标就是降低振荡器的功耗，而方法就是采用不够稳定的环路振荡器，这种类型的振荡器由非门中的`PMOS`和`NMOS`晶体管的门电压来控制每个非门延时时间，另外在非门之间添加RC电路来实现额外的延迟。  
- 作者利用`HSPICE`（一种用来仿真电路的开源软件）来观察为了生成20MHz的振荡器所需要的V<sub>nc</sub>、V<sub>pc</sub>和RC参数。  
- 由于环路振荡器对温度敏感，几十度的温度变化会导致几MHz的频率变化，但是体表传感器的温度会控制在36.6到37.2度之间，即使出汗的情况下，根据热力学定律温度的变化也会控制在1摄氏度之内。在`HSPICE`中，测量的频率变化出于69-210kHz之间。  
- 对于WiFi信号，超过150kHz会导致吞吐量的下降，到达250kHz的偏移时吞吐量会降到零。对于BLE来说450kHz的偏移会导致吞吐量的下降。而且基于bit的传输要比基于分组的传输效果更好。  
- 另外一个优化的方向就是降低电压，ADG902需要的最低电压是1.65V，但实际上可以将其控制在1.0725到0.35V之间。环路振荡器和数据调制器也可以降低电压来降低功耗。  

**`4. IMPLEMENTATION`**  
- FS-Backscatter的架构分为三个部分：  
  1. `FS-Backscatter Tags`：使用了一个ADG902晶体管用于调制天线，天线通过SMA连接器连接到晶体管上，通过使用SMA接头可以连接不同的天线。本文使用了一个`VERT2450`和一个`TL-ANT2409A 2.4GHz`天线来反射2.4GHz无线信号。除了以上的原型，还有一个FS-Backscatter在HSPICE中全仿真。  
  2. `Active transmitter and FS-Backscatter decoder`：发射器就是简单的Bluetooth/BLE或者WiFi发射器，用于在特定的频道持续不断的广播数据。基于分组的解码器使用了商业`TI CC3200 WiFi`接收器和`TI CC2650 BLE`接收器。基于比特的接收器使用了`TI CC2541 BLE`芯片组，因为其除了支持正常的BLE工作模式，还支持绕过蓝牙协议栈的专有模式，这样可以直接访问频道的RSSI，这一组APIs并不是广泛有效的。所以，如果下一代使用了这种芯片组的健康腕带或智能手表可以让Backscatter以更快的速率进行传输。  
  3. `WiFi Backscatter setup`：标签上使用了9dBi直向天线用于对比，本文的其他实验使用的都是3dBi的`omni-directional`天线。而发送器和接收器全都是用的标准芯片或PCB天线。  

**`5. EVALUATION`**  
- **`5.1 FS-Backscatter: Throughput and BER`**  
- 从吞吐量来说，由于`FS-Backscatter`拥有一个干净的波段，因此各项指标均优于`WiFi Backscatter`。因为基于分组的方式的信号具有结构，所以其工作距离更长、错误率上升更为缓和。而基于比特的方式则速度更快。  
- **`5.2 Multiple Carriers and Receivers`**  
- 利用多个发射器增强信号的强度，可以使数据速率提高1.47倍。  
- 而利用多个接收器进行协同解码，可以提高数据速率1.24倍，这是因为一个接收器的信号较弱，但另外一个可能会比较强。  
- **`5.3 Power consumption`**  
- 在没有改变电压，使用ADG902最低电压1.65V的情况下，环路振荡器、数据调制器和RF晶体管分别消耗 78μW、11.5μW和57.1μW，在50kbps的传输下总功耗为146.6μW。  
- 在改变电压，使用低电压0.8V的情况下，环路振荡器、数据调制器和RF晶体管分别消耗 20.8μW、0.1μW和24.1μW，在50kbps的传输下总功耗为45μW。主要节省的电能来自于环路振荡器。  
- **`5.4 FS-Backscatter vs BLE/Zigbee`**  
- 低功耗通信的衡量主要通过两个参数：比特/焦耳和峰值功率。因为峰值功率会影响到电池的续航。  
- **`5.5 Mobile and static deployment`**  
- 在移动情况下数据速率要低于静止的状态，但仍然比大部分传感器的需求要高。  
- **`5.6 Mutual Interference`**  
- 通过实验，10米以内的WiFi信号将导致Backscatter的传输速率降到0。相反标签与接收器之间的距离在1米时，标签对WiFi信号基本没有影响。其原因就是FS-Backscatter的信号强度很低。  

**`6. DISCUSSION`**  
- `MAC protocol`：由于CSMA（载波侦听多路访问）需要消耗的能量是毫瓦级，因此无法再标签上实现这个功能。但是可以将该功能迁移到通讯的发起者上，其流程为：  
  1. 发起者侦听有效的无线频谱，至少需要侦听两个相邻的频道，一个用于发射原始载波，一个用于FS-Backscatter标签。  
  2. 如果有可用频道，则发送RTS-CTS之类的消息保留这两个频道，然后通知标签开始进行通讯。由于只能保留频道一段时间，必须将这个时间窗口可发的数据量通知FS-Backscatter标签。  
- `Interscatter`与`FS-Backscatter`的比较：前者只支持蓝牙发射端和WiFi接收端；而且需要生成WiFi或Bluetooth的基带信号；但是前者的单边波段调制技术可以被用于FS-Backscatter；此外前者使用PLL（`Phase Lock Loop`）耗能更高。  
- `Reducing tag power consumption`：如果仅面向蓝牙技术，则仅需要频移2-4MHz，因此振荡器可以节省大量的能耗。  
