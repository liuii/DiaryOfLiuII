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