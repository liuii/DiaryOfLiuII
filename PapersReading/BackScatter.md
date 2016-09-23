# BackScatter相关论文阅读手记  

#### `SigComm16G08P01` `2016-09-23` Enabling Practical Backscatter Communication for On-body Sensors  
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
  1. 


















