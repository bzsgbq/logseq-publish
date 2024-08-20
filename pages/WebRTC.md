- ## MyNotes
	- WebRTC 是什么?
		- WebRTC的全称是网页实时通信
		  collapsed:: true
			- 它通过简单的API, 为浏览器, 移动平台以及物联网设备提供高质量的实时通信功能
		- 核心点是, 两个浏览器之间点对点通信, 即在真正传输音视频数据时不需要中间服务器, 从而实现低延迟
		  collapsed:: true
			- 首先要建立点对点连接 (`RTCPeerConnection`)
			  collapsed:: true
				- 通过信令服务器进行媒体协商和网络协商
					- 媒体协商
						- SDP offer  &  SDP answer
						- 通信双方彼此告知对方自己的的媒体配置: 编解码器, 视频格式, 使用的传输协议等
							- communication protocols, channels, media codecs and formats, and method of data transfer, as well as any required routing information.
					- 网络协商
						- 通信双方彼此告知对方自己的 ICE candidates (自己的外网IP和端口号)
						- 首先得通过 ICE server (要么是STUN server, 要么是TRUN server), 各自获取到自己的 ICE candidates, 然后才能告知对方
							- 对于非对称NAT, 可以直接通过STUN获取到外网IP地址和端口号
							- 有的NAT路由器会有 "对称NAT" 的限制, 即只会允许之前连接过的peers再建立连接, 这就会导致就算STUN获取到了外网IP和端口号, 也不可用. 这时就需要TURN服务器做中继
			- 然后开始彼此传输数据 (`RTCDataChannel`)
			  collapsed:: true
				- ((66154b5b-efe5-48c4-8c9c-9024d3631841))
	- ~~WebRTC工作原理~~
	  collapsed:: true
		- ~~[Why WebRTC｜“浅入深出”的工作原理详解 - 专栏 - 声网开发者社区 (rtcdeveloper.cn)](https://www.rtcdeveloper.cn/cn/community/blog/22628)~~
		  collapsed:: true
			- 关于WebRTC的工作流程，我们从“如何实现一个 1 对 1 通话”场景来看可能会更直观一些：
				- ![image.png](../assets/image_1711447674877_0.png){:width 666}
				- 双方先调用 getUserMedia 打开本地摄像头；
				- 向信令服务器发送加入房间请求；
				- Peer B 接收到 Peer A 发送的 offer SDP 对象，并通过PeerConnection的SetLocalDescription方法保存 Answer SDP 对象并将它通过信令服务器发送给 Peer A。
				- 在 SDP 信息的 offer/answer 流程中，Peer A 和 Peer B 已经根据 SDP 信息创建好相应的音频 Channel 和视频 Channel，并开启Candidate 数据的收集，Candidate数据（本地IP地址、公网IP地址、Relay服务端分配的地址）。
				- 当 Peer A 收集到 Candidate 信息后通过信令服务器发送给 Peer B。同样的过程 Peer B 对 Peer A 也会再发送一次。
			- 这样 Peer A 和 PeerB 就相互交换了媒体信息及网络信息，如果能达到一致(找到交集)，就可以开始通讯了。
		- [WebRTC入门指南 —— 实现一个完整的点对点视频通话（信令服务器+客户端） - 掘金 (juejin.cn)](https://juejin.cn/post/7071994793710075911)
			- trivial
				- ![image.png](../assets/image_1711460335851_0.png){:width 555}
				- 我们可以看到，一个简单的点对点通讯系统主要由四部分组成：
					- WebRTC客户端：负责生产/消费音视频数据，位于NAT之内，属于内网
					- NAT：Network Address Translation (NAT)，网络地址转换协议， 能够将设备的内网地址映射到一个公网的地址。
					- 信令服务器：用于传输SDP、candidate等信令数据。
					- STUN/TURN服务器(中继服务器)：
					- STUN：用于为位于NAT内的设备找到自己的公网地址。WebRTC客户端通过给处于公网的STUN服务器发送请求来获得自己的公网地址信息，以及是否能够被（穿过路由器）访问。
					- TURN：对于无法通过STUN服务器进行内网穿越的“对称型NAT”，我们可以借助TURN服务器作为中继服务器，通过TURN服务器对数据进行转发。
			- ==点对点的通信原理：==
			  collapsed:: true
				- 图示:
					- ![image.png](../assets/image_1711460963121_0.png){:width 666}
				- 首先, 通信双方都与信令服务器建立连接, 方便之后进行媒体协商和网络协商
					- 首先客户端需要信令服务器连接，后续双方需要通过信令服务器来了解对方的一些必要的信息，比如告诉对方自己的支持的音视频编解码格式、自己外网IP地址和端口是多少等（此时还无法知道自己的公网地址）。
				- 媒体协商
					- send offer sdp --> signaling server --> relay offer sdp
					- relay answer sdp <-- signaling server <-- send answer sdp
				- 与STUN建立连接，获得自己的外网IP地址和端口，以及是否能够进行内网穿越。不支持内网穿越的情况下还需要连接TURN服务器进行中继通信。
				- WebRTC客户端拿到自己的外网IP地址和端口后，通过信令服务器将自己的信息（candidate信息）交换给对方。当双方都获取到对方的地址后，它们就可以尝试NAT穿越，进行P2P连接了。
	- ~~WebRTC特点 (和不同的web服务相比)~~
	  collapsed:: true
		- Peer2Peer (点对点)
			- 两个客户端浏览器直接进行通信, 而不需要服务器
			- 当然不是完全不需要; 在最开始, 它们还是需要先通过信令服务器找到彼此
	- ~~WebRTC架构~~
	  collapsed:: true
		- ![image.png](../assets/image_1711447521353_0.png){:width 666}
	- 算法
		- lgt
		  collapsed:: true
			- GCC
			  collapsed:: true
				- 全名Google Congestion Control
				- GCC包括基于延迟的算法和基于丢包的算法。 {{cloze 发送端收到接收端反馈的Feedback事件，可以计算出延时梯度变化 (就是延时差的变化)，链路丢包率，基于此估算出两个可用带宽，取其中较小的带宽值作为链路的可用带宽。}}
					- Delay-based Control Congestion Algorithm
						- 包组延时评估(inter arrival)
							- 计算每个包组的延时差值
						- 到达时间滤波器(arrival-time filter)
							- 线性回归进行时延梯度预测，通过最小二乘法求拟合直线的斜率
						- 过载检测器(over-use detector)
							-
						- 码率控制器(AIMD)
					- Loss-based Congestion Control Algorithm
						- 核心思想：产生丢包说明网络开始发生拥塞
			- Jitter Buffer
			  collapsed:: true
				- 定义
					- 什么是抖动? 实时音视频基于UDP协议, 会因为网络延迟, 丢包等, RTP数据包到达接收器的时间不稳定, 这就是抖动.
					- jitter buffer 就是通过在接收端设置缓冲区，存储和调整接收到的数据包的顺序，从而减少抖动。
					- jitter buffer将来自网络的不均匀的乱序的数据包进行缓存, 并将均匀的排好序的帧传送到解码器进行解码
				- 类型
					- 固定大小的Jitter Buffer:这是最简单的算法，其中为Jitter Buffer设置了固定大小(即它可以存储的数据包数量)。该算法实现简单，但对网络环境的适应能力较差。
					- 自适应Jitter Buffer:该算法根据网络情况动态调整Jitter Buffer的大小。例如，当检测到明显的网络延迟波动时，可以增加Jitter Buffer的大小以减少丢包的可能性。相反，当网络条件良好时，可以减小Jitter Buffer的大小以最小化播放延迟。
					- 基于延迟预测的Jitter Buffer:该算法通过预测接收到的数据包的延迟来确定何时从Jitter Buffer检索数据包进行播放。
					- 基于丢包率的Jitter Buffer:根据接收端的丢包率调整Jitter Buffer的大小。当丢包率较高时，可以增加Jitter Buffer的大小，降低丢包的可能性。相反，当丢包率较低时，可以减小Jitter Buffer的大小，以降低播放延迟。
				- 步骤
				  collapsed:: true
					- 接收RTP报文:
						- WebRTC接收到RTP报文后，将RTP报文存储在Jitter Buffer中。
					- 分组排序:
						- 根据分组的序列号和时间戳对分组进行排序，以确保正确的播放顺序。
					- 动态调整jitter buffer大小:
						- 根据时延、丢包率等网络情况，动态调整Jitter Buffer的大小。这可以使用通过RTCP收集的统计信息来实现。
					- 丢包处理:
						- 如果检测到丢包，则发送NACK报文请求发送方重传该报文。
					- 包检索:
						- 根据数据包的时间戳和当前播放时间，从Jitter Buffer中检索数据包进行解码和播放。
					- 同步处理:
						- 在音频-视频同步的情况下，可能需要根据音频和视频之间的延迟差异调整Jitter Buffer的行为，以尝试同步恢复。
			- NACK
			  collapsed:: true
				- WebRTC在RTT比较小的时候, 会采用NACK来进行丢包补偿.
				- 即接收端发现丢包之后, 可以向发送端发送NACK消息, 请求重传
				- 问题
					- 这个过程有一个问题是在网络抖动和丢包很厉害的情况下有可能造成同一时刻收到很多NACK的重传请求，发送端瞬间把这些重传请求放入pacer中进行重发，这样pacer的延迟会增大，而且pace的参考码率会随着pace queue的延迟控制变的很大而出现间歇性网络风暴。
					- WebRTC在处理NACK重传时设计了一个重传码率控制器，设计原理是通过统计单位时间窗口周期中发送的字节数据来限流，如果这个时间窗内发送的数据的码率大于estimator评估的码率，不进行当前NACK请求的重传，等待下一个NACK。
			- FEC
			  collapsed:: true
				- WebRTC在RTT很大时候会开启FEC来进行丢包补偿。主要的思想就是通过==增加冗余数据来让接收端能对丢失的数据包进行恢复。==
				- 发送端对原始数据进行 FEC 编码，生成冗余奇偶校验数据包，原始数据包和冗余数据包的数量比例是固定的。接收端接收到 FEC 数据块后，通过冗余数据包和原始数据包来恢复出丢失或者出错的数据包。FEC 编解码算法目前比较成熟的为：里得所罗门算法、Raptor 算法和 Tornado 算法。
		- 带宽探测
			- ~~带宽估计需要带宽侦测~~
			  collapsed:: true
				- 在WebRTC中，带宽侦测是通过周期性地发送数据包并测量其到达时间来完成的。WebRTC中的带宽侦测通常涉及以下步骤：
					- 发送数据包: WebRTC会周期性地向对方发送一些数据包，通常称为“probe packets”。这些数据包的大小和发送间隔可以根据应用程序需求进行调整。
					- 计算延迟: 当对方收到数据包时，会立即将其回传到发送方。发送方会测量从发送数据包到收到回传数据包所需的时间，从而计算出延迟。
					- 计算数据传输速度: 根据延迟和数据包大小，WebRTC可以计算出网络连接的实际带宽。可以在多个时段进行带宽侦测，以确定带宽的平均值。
					- 动态调整数据传输速率: 在WebRTC中，根据实时带宽侦测的结果，可以动态调整音频和视频流的数据传输速率。这可以确保在网络质量变差时，WebRTC应用程序仍然可以保持稳定的通信。
				- WebRTC使用了一种名为REMB（Receiver Estimated Max Bitrate，接收方估计的最大比特率）的机制来进行带宽侦测。
					- 在WebRTC中，每个参与通信的客户端都会周期性地向对方发送一个RTCP Feedback包，并在该包中包含自己对对方视频流的带宽需求，即期望的最大比特率。接收方收到RTCP Feedback包后，会根据其中包含的带宽需求和自身的网络状况，发送一个Feedback包回复发送方，告诉其可以发送的最大比特率。
					- 通过这种方式，WebRTC可以在客户端之间交换网络状况和带宽需求信息，从而动态地调整数据传输速率，以适应不同的网络环境。
				- WebRTC中的TWCC（Transport Wide Congestion Control，传输层拥塞控制）是一种新的拥塞控制机制，与传统的基于REMB的带宽侦测相比，具有更好的性能和鲁棒性。
					- 在传统的REMB机制中，发送方发送一个Feedback包给接收方，接收方根据其中包含的带宽需求和自身的网络状况，发送一个Feedback包回复发送方，告诉其可以发送的最大比特率。这种机制对网络状况的响应速度较慢，容易出现拥塞控制不够及时的情况，从而影响通信质量。
					- TWCC机制引入了一种新的拥塞控制机制，使用了一种名为“Feedback PacketInformation”（FPI）的机制来收集数据。通过在Feedback包中包含FPI信息，TWCC可以更准确地估计网络的拥塞程度，并及时地调整发送速率，以避免网络拥塞。
					- TWCC机制的另一个优势是可以对每个视频流进行独立的拥塞控制。这样，在多路视频通信的情况下，TWCC可以更准确地调整每个视频流的发送速率，以适应不同的网络环境，从而提高通信质量和稳定性。
					- 总之，WebRTC的TWCC机制相比传统的REMB机制，具有更好的性能和鲁棒性。但需要注意的是，TWCC机制在一些老旧的网络环境下可能会遇到兼容性问题，因此在实际应用中需要进行充分的测试和评估。
			- ~~webrtc带宽预测中的Probe（探测）模块~~
			  collapsed:: true
				- 为什么需要带宽探测
				  collapsed:: true
					- webrtc使用gcc（google congestion control）来估计带宽，控制当前的发送速度。gcc中基于丢包和基于延迟的算法有个特点：
						- 1.能迅速响应带宽的衰减
						- 2.不能迅速响应带宽的增加
					- 举个例子，
						- 如果当前带宽是20mbps，由于其它因素，带宽骤降至15mbps，gcc能快速的给出over-using信号(rtt为10ms环境下，响应速度是100-200ms)。
						- 如果当前带宽是20mbps，由于其它因素，带宽升至了25mbps，依赖gcc自身的调整的话需要几十秒的时间（加性增减性乘的效果）。
					- 由于以上的原因，如果从0bps开始运行gcc算法（纯算法本身），要达到一定的带宽需要太长的时间。
					- webrtc中有一个用来在起始阶段（或周期性）迅速探测到当前带宽的措施—就是Probe模块，和TCP的慢启动比较像啦！
				- 原理
					- Probe的原理简单说起来就是这样的：发送端以一定的速度发送数据包，同时记录这些数据包的发送时间、序列号（全局唯一）、探测组的id.
					- 接收端每过一段时间（50-150ms）会反馈数据包的到达时间，就像这样：
			- webrtc模块之带宽预测Prober_bitrateprober__getnextprobetime-CSDN博客
				- 引入prober的目的
					- 因为GCC算法对带宽的衰减比较敏感，而对于带宽的增加反应缓慢
						- 比如说带宽开始为10mbps，突然降8mbps，gcc会很快做出反应，网络处于过载状态，而如果带宽开始为10mbps，突然升到50mbps，由于gcc处理带宽上升时，远离收敛时是乘性增加，逼近收敛时是加性增加，时间会比较长，可能要1-2秒，会影响数据发送。此时如果使用prober，就会很快的从10mbps，上升到50mbps，可能只需要500ms左右，prober的作用显而易见。
					- 从GCC算法本身来说，在初始化时或者网络带宽提升时, 会按照乘性增大或者加性增大, 慢慢提升到目标码率，过程较慢. 所以我们可以在发送端进行带宽探测, 快速探测链路容量上限
				- 什么时候开启prober探测
					- 最开始时
					- 状态从Hold转化为Incr.时
					- 周期性判断
					- 乘性探测
						- 为了快速的探测到实际带宽的大致值，使用乘性探测。gcc会根据探测码率、基于延时的码率、基于丢包的码率和基于当前的码率等综合输出其中最低的码率作为pacer或编码器的发送码率target_bitrate，此时如果探测码率大于target_bitrate的70%，则继续开启探测。举个例子，假设起始速度设置为 500kbps，那么探测速度就设置为 1Mbps，如果探测结果prober_bitrate大于1Mbps* 0.7 =700kbps ，继续向上探测，探测目标重新设置为 2Mbps，如果第二次探测结果prober_bitrate大于 2Mbps *0.7=1.4Mbps，继续向上探测，探测目标重新设置为 4Mbps……直到某一个探测结果prober_bitrate小于所设置的探测目标乘以0.7，那么就判定链路带宽应该在此次探测结果附近。
				- 原理 \\\\\\
					- 设定 (ProbeClusterConfig) (ProbeCluster就是探测包组)
						- at_time: 何时进行探测
						- target_data_rate (目标码率)
						- target_duration (探测时间)
						- target_probe_count (探测包数)
						- id (探测包组id)
						- 其它
							- 程序启动后便开启prober探测，首先确定起始码率，webrtc设置为300kbps，探测目标设置两个探测阶段，为三倍起始码率900kbps，第二阶段探测码率为2倍第一阶段探测码率，为1.8Mbps。
					- 将ProbeClusterConfig传给pacer, pacer会定时生成探测包发送事件
						- 将ProbeClusterConfig, 作为平滑发送模块pacer中BitrateProber的探测配置，这样的话在探测激活状态下，每发送一个数据包也都是探测包，直到发送的包字节数超过本次探测的最小应发送的字节，本次探测结束。
						- `BitrateProber::CreateProbeCluster`
							- time_created_ms = now_ms (创建时间)
							- pace_info.probe_cluster_min_probes (最少发送探测包数)
							- pace_info.probe_cluster_min_bytes (最少发送字节数)
							- pace_info.send_bitrate_bps = bitrate_bps (探测目标码率)
							- pace_info.probe_cluster_id = cluster_id (探测包组id)
						- 具体是如何生成探测包发送事件的 (如何设置发包间隔)
							- pacer发送包是按5ms发送一次，探测包的发包间隔是可变的，发送端会控制探测包的发送速度，因此探测包间隔大于5ms也可能小于5ms，优先级是探测包间隔，意思是：如果探测包间隔是4ms，则按4ms到达发送包。
							- 发包间隔时间的计算方式
								- 码率bitrate=发送字节/时间间隔delta_ms, 因此delta_ms=发送字节/码率
					- sender接收到发送事件之后, 会进行探测包的发送
					- receiver会识别出探测包, 并进行计算:
						- ![image.png](../assets/image_1713095413528_0.png){:width 400,:height 800}
		- 拥塞控制
			- 参考
			  collapsed:: true
				- [[@Analysis and design of the google congestion control for web real-time communication (WebRTC)]]
				- ==[WebRTC拥塞控制算法——GCC介绍 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/454893807)==
			- 拥塞控制循环
			  collapsed:: true
				- estimator 根据RTCP中的延迟梯度信息和丢包信息, 评估网络拥塞状态, 计算出适合当前网络的码率
				- 改变 视频编解码 码率
				- pacer (发送配置器) 根据码率, 改变触发发送事件的速度
				  collapsed:: true
					- pacer会根据这个码率, 改变pacer的网络发送速度和padding比例, 并使用新的网络发送速度来定时触发发包事件
				- sender收到pacer的发送事件, 进行RTP报文发送
				- receiver收到send发送的RTP报文, 进行arrival time统计和丢包统计
				- feedback对定时对receiver统计的信息进行RTCP编码, 并反馈到发送端的estimator, 进行新一轮的码率评估
				-
			- GCC
			  collapsed:: true
				- 利用延迟梯度和丢包率估计网络可用带宽, 并根据可用带宽控制发送速率. 因此GCC包含基于延迟的算法和基于丢包的算法
					- 基于延迟
						- **数据收集**: [[$red]]==包组延时评估 (inter arrival)==
							- 计算相邻包组 "到达时间的差值" 和 "发送时间的差值"
							- 使用数据包分组为单位计算延迟可以避免==突发数据==对于带宽估计准确性的影响
						- **数据预处理**: (计算延迟梯度) [[$red]]==到达时间滤波器 (arrival-time filter)==
							- 滤波平滑: 使用到达时间差值减去发送时间差值, 得到传输延迟, 并通过卡尔曼滤波器做平滑
							- 使用线性回归进行延迟梯度预测 (即通过最小二乘法计算直线斜率, 斜率就是trend)
						- [[$red]]==过载检测器 (over-use detector)==
						  collapsed:: true
							- 将trend与动态的threshold进行比较, 判断当前要发送的信号:
								- overuse (过载)
								- underuse (欠载)
								- normal (正常)
							- 动态threshold的基本调整思想
								- 当延迟梯度减小时，动态阈值会以一个更慢的速率减小；
								- 当延迟梯度增大时，动态阈值会以一个更慢的速度增大；
								- 相对而言， 阈值的减小速度要小于增大速度。
							- 动态threshold的具体计算方式
								- ![image.png](../assets/image_1713084941219_0.png){:width 666}
								-
							- 为什么需要动态threshold?
								- 【Gcc-analysis】给出了两个理由，不过第一个理由貌似用自适应阈值也很难解决，应该采用丢包率来评估带宽，主要应该还是第二个理由。第二个问题应该是GCC和BBR拥塞控制算法一个核心要解决的问题。TCP的拥塞控制算法主要依据是丢包重传，而当网络出现丢包往往是中间路由器buffer被填满的时候，此时网络传输延时比较大。而像GCC和BBR这种基于延时来判断网络拥塞的算法，期望达到的效果是在延时最低而网络吞吐量达到最大，很难竞争过TCP的这种流氓算法，如果一味避让最终必然会被饿死，所以GCC在设计时，在和TCP竞争时会适当提高阈值。(其实就是TCP流量在把路由器队列占满之后才会进行拥塞控制, 如果不采用动态阈值, 而是设定成一个较小的值, 那算法就会始终判断成过载, 去降到最低码率)
								  collapsed:: true
									- ![image.png](../assets/image_1713087326532_0.png){:width 400,:height 800}
						- [[$red]]==码率控制器 (AIMD)==
							- 根据速率控制状态机，按照==和式增加、积式减少==的原则估算出当前的网络速率
							- ![image.png](../assets/image_1713082947459_0.png){:width 400,:height 800}
							- 什么情况下, 会受到什么信号, 进入什么状态
								- 当网络拥塞时，收到 overuse 信号，说明网络拥塞很严重，状态机进入Decr状态，降低发送码率。
								- 当路由器队列中的数据包被快速释放时，收到underuse信号，状态机进入 Hold状态，保持码率，继续排空。
								- 当网络平稳时，收到normal信号，状态机进入 Incr状态，开始探测是否可以增加发送码率。
							- ![image.png](../assets/image_1713083065484_0.png){:width 400,:height 800}
							- 更详细
								- 当前是Incr 状态，如果 "吞吐量 (Rг)" 和 "链路容量"（历史吞吐量的指数平滑）相差较大，则对当前码率（上次更新的码率）使用乘性增加 (1.05)；如果相差较小，则使用加性增加。
								- 当前是Decr状态，直接将 "当前吞吐量 x 0.85" 作为新码率，如果该码率可能仍大于上一个调整后的码率，则使用 "链路容量 x 0.85" 作为新码率。
					- 基于丢包
						- 根据丢包率判断网络拥塞情况
							- 丢包率很大：网络拥塞状况严重, 需要减小码率
							- 丢包率很小或为0：网络状态良好，可以适当增大码率
							- 其他情况：发送码率保持不变
							- ![image.png](../assets/image_1713084047067_0.png){:width 666}
							- ![image.png](../assets/image_1713084092894_0.png){:width 400,:height 800}
					- 取两种算法所选择的码率的最小值
-
- [有了WebRTC，直播可以这样玩！ - 掘金 (juejin.cn)](https://juejin.cn/post/6964571538729205773)
  collapsed:: true
	- WebRTC全称Web Real-time Communication，网页即时通讯技术。它是由Google发起的一个的实时通讯解决方案。之所以称为一个方案，而不是协议，是因为它涵盖了音视频采集、通讯的建立、信息传输、音视频显示等整套的实现方案。该方案的发起让加快速地实现一个音视频通讯应用成为可能。
	-
- [WebRTC官方网站](https://webrtc.org/)
  collapsed:: true
	- 借助 WebRTC，您可以为应用添加基于开放标准运行的实时通信功能。它支持在对等设备之间发送视频、语音和通用数据，使开发者能够构建强大的语音和视频通信解决方案。
	- 这项技术适用于所有现代浏览器以及所有主要平台的原生客户端。WebRTC 采用的技术是开放网络标准，以常规 JavaScript API 的形式在所有主流浏览器中提供。对于原生客户端（例如 Android 和 iOS 应用），可以使用具备相同功能的库。
	- WebRTC 项目属于[开源项目](https://webrtc.googlesource.com/src/)，受 Apple、Google、Microsoft 和 Mozilla 等公司支持。本页面由 Google WebRTC 团队维护。
-
- Overview
  collapsed:: true
	- [Getting started with WebRTC](https://webrtc.org/getting-started/overview)
		- 如果您不熟悉这些api，那么基于WebRTC技术创建一个新的应用程序可能会让您不知所措。在本节中，我们将通过解释一些常见的用例和解决这些用例的代码片段，展示如何开始使用WebRTC标准中的各种api。
		- ## WebRTC APIs
			- 在高层次上，WebRTC标准涵盖了两种不同的技术: media capture devices and peer-to-peer connectivity
				- Media capture devices includes video cameras and microphones, but also screen capturing "devices".
					- For cameras and microphones, we use  `navigator.mediaDevices.getUserMedia()`  to capture  `MediaStreams` .
					- For screen recording, we use  `navigator.mediaDevices.getDisplayMedia()`  instead.
				- The peer-to-peer connectivity is handled by the  `RTCPeerConnection`  interface. ==This is the central point for establishing and controlling the connection between two peers in WebRTC.==
- Get started
  collapsed:: true
	- [Getting started with media devices  |  WebRTC](https://webrtc.org/getting-started/media-devices)
	  collapsed:: true
		- When developing for the web, the WebRTC standard provides ==APIs for accessing cameras and microphones== connected to the computer or smartphone. These devices are commonly referred to as Media Devices and can be accessed with JavaScript through the  `navigator.mediaDevices`  object, which implements the  `MediaDevices`  interface. From this object we can enumerate all connected devices, listen for device changes (when a device is connected or disconnected), and open a device to retrieve a Media Stream (see below).
		- The most common way this is used is through the function  `getUserMedia()` , which returns a promise that will resolve to a  `MediaStream`  for the matching media devices. This function takes a single  `MediaStreamConstraints`  object that specifies the requirements that we have. For instance, to simply open the default microphone and camera, we would do the following.
		-
	- [Media capture and constraints  |  WebRTC](https://webrtc.org/getting-started/media-capture-and-constraints)
	- [Getting started with peer connections  |  WebRTC](https://webrtc.org/getting-started/peer-connections)
		- Peer connections is the part of the WebRTC specifications that deals with connecting two applications on different computers to communicate using a peer-to-peer protocol. The communication between peers can be video, audio or arbitrary binary data (for clients supporting the  `RTCDataChannel`  API). ==In order to discover how two peers can connect, both clients need to provide an ICE Server configuration. This is either a STUN or a TURN-server, and their role is to provide ICE candidates to each client which is then transferred to the remote peer. This transferring of ICE candidates is commonly called signaling.==
		- ## Signaling
		  collapsed:: true
			- The WebRTC specification includes APIs for communicating with an ICE (Internet Connectivity Establishment) Server, but the signaling component is not part of it. Signaling is needed in order for two peers to share how they should connect. Usually this is solved through a regular HTTP-based Web API (i.e., a REST service or other RPC mechanism) where web applications can relay (中继) the necessary information before the peer connection is initiated.
			- The follow code snippet shows how this fictious signaling service can be used to send and receive messages asynchronously. This will be used in the remaining examples in this guide where necessary.
			- ```js
			  // Set up an asynchronous communication channel that will be
			  // used during the peer connection setup
			  const signalingChannel = new SignalingChannel(remoteClientId);
			  signalingChannel.addEventListener('message', message => {
			      // New message from remote client received
			  });
			  
			  // Send an asynchronous message to the remote client
			  signalingChannel.send('Hello!');
			  ```
			- Signaling can be implemented in many different ways, and the WebRTC specification doesn't prefer any specific solution.
		- ## Initiating peer connections
		  collapsed:: true
			- Each peer connection is handled by a  `RTCPeerConnection`  object. The constructor for this class takes a single  `RTCConfiguration`  object as its parameter. This object defines how the peer connection is set up and should contain information about the ICE servers to use.
			- Once the  `RTCPeerConnection`  is created we need to create an SDP offer or answer, depending on if we are the calling peer or receiving peer. [[#red]]==Once the SDP offer or answer is created, it must be sent to the remote peer through a different channel.== ==Passing SDP objects to remote peers is called signaling and is not covered by the WebRTC specification.==
			- To initiate the peer connection setup from the calling side, we create a  `RTCPeerConnection`  object and then call  `createOffer()`  to create a  `RTCSessionDescription`  object. This session description is set as the local description using  `setLocalDescription()`  and is then sent over our signaling channel to the receiving side. We also set up a listener to our signaling channel for when an answer to our offered session description is received from the receiving side.
			- ```js
			  async function makeCall() {
			      const configuration = {'iceServers': [{'urls': 'stun:stun.l.google.com:19302'}]}
			      const peerConnection = new RTCPeerConnection(configuration);
			      signalingChannel.addEventListener('message', async message => {
			          if (message.answer) {
			              const remoteDesc = new RTCSessionDescription(message.answer);
			              await peerConnection.setRemoteDescription(remoteDesc);
			          }
			      });
			      const offer = await peerConnection.createOffer();
			      await peerConnection.setLocalDescription(offer);
			      signalingChannel.send({'offer': offer});
			  }
			  ```
			- On the receiving side, we wait for an incoming offer before we create our  `RTCPeerConnection`  instance. Once that is done we set the received offer using  `setRemoteDescription()` . Next, we call  `createAnswer()`  to create an answer to the received offer. This answer is set as the local description using  `setLocalDescription()`  and then sent to the calling side over our signaling server.
			- ```js
			  const peerConnection = new RTCPeerConnection(configuration);
			  signalingChannel.addEventListener('message', async message => {
			      if (message.offer) {
			          peerConnection.setRemoteDescription(new RTCSessionDescription(message.offer));
			          const answer = await peerConnection.createAnswer();
			          await peerConnection.setLocalDescription(answer);
			          signalingChannel.send({'answer': answer});
			      }
			  });
			  ```
			- ==Once the two peers have set both the local and remote session descriptions they know the capabilities (能力) of the remote peer.  This doesn't mean that the connection between the peers is ready.  For this to work we need to collect the ICE candidates at each peer and transfer (over the signaling channel) to the other peer.==
		- ## ICE candidates
		  collapsed:: true
			- Before two peers can communicate using WebRTC, they need to exchange connectivity information. Since the network conditions can vary depending on a number of factors, an external service is usually used for discovering the possible candidates for connecting to a peer. This service is called ICE and is using either a STUN or a TURN server. STUN stands for Session Traversal Utilities for NAT, and is usually used indirectly in most WebRTC applications.
			- TURN (Traversal Using Relay NAT) is the more advanced solution that incorporates the STUN protocols and most commercial WebRTC based services use a TURN server for establishing connections between peers. The WebRTC API supports both STUN and TURN directly, and it is gathered under the more complete term Internet Connectivity Establishment. When creating a WebRTC connection, we usually provide one or several ICE servers in the configuration for the  `RTCPeerConnection`  object.
			- ### Trickle ICE
				- Once a  `RTCPeerConnection`  object is created, the underlying framework uses the provided ICE servers to gather candidates for connectivity establishment (ICE candidates). The event  `icegatheringstatechange`  on  `RTCPeerConnection`  signals in what state the ICE gathering is ( `new` ,  `gathering`  or  `complete` ).
				- While it is possible for a peer to wait until the ICE gathering is complete, it is usually much more efficient to use a "trickle ice" technique and transmit each ICE candidate to the remote peer as it gets discovered. This will significantly reduce the setup time for the peer connectivity and allow a video call to get started with less delays.
				- To gather ICE candidates, simply add a listener for the  `icecandidate`  event. The  `RTCPeerConnectionIceEvent`  emitted on that listener will contain  `candidate`  property that represents a new candidate that should be sent to the remote peer (See Signaling).
				- ```js
				  // Listen for local ICE candidates on the local RTCPeerConnection
				  peerConnection.addEventListener('icecandidate', event => {
				      if (event.candidate) {
				          signalingChannel.send({'new-ice-candidate': event.candidate});
				      }
				  });
				  
				  // Listen for remote ICE candidates and add them to the local RTCPeerConnection
				  signalingChannel.addEventListener('message', async message => {
				      if (message.iceCandidate) {
				          try {
				              await peerConnection.addIceCandidate(message.iceCandidate);
				          } catch (e) {
				              console.error('Error adding received ice candidate', e);
				          }
				      }
				  });
				  ```
				-
			-
		- ## Connection established
		  collapsed:: true
			- Once ICE candidates are being received, we should expect the state for our peer connection will eventually change to a connected state. To detect this, we add a listener to our  `RTCPeerConnection`  where we listen for  `connectionstatechange`  events.
			- ```js
			  // Listen for connectionstatechange on the local RTCPeerConnection
			  peerConnection.addEventListener('connectionstatechange', event => {
			      if (peerConnection.connectionState === 'connected') {
			          // Peers connected!
			      }
			  });
			  ```
	- [Getting started with remote streams  |  WebRTC](https://webrtc.org/getting-started/remote-streams)
	  collapsed:: true
		- Once a  `RTCPeerConnection`  is connected to a remote peer, it is possible to stream audio and video between them. This is the point where we connect the stream we receive from  `getUserMedia()`  to the  `RTCPeerConnection` . A media stream consists of at least one media track, and these are individually added to the  `RTCPeerConnection`  when we want to transmit the media to the remote peer.
		- ```js
		  const localStream = await getUserMedia({video: true, audio: true});
		  const peerConnection = new RTCPeerConnection(iceConfig);
		  localStream.getTracks().forEach(track => {
		      peerConnection.addTrack(track, localStream);
		  });
		  ```
		- Tracks can be added to a  `RTCPeerConnection`  before it has connected to a remote peer, so it makes sense to perform this setup as early as possible instead of waiting for the connection to be completed.
		- ## Adding remote tracks
			- To receive the remote tracks that were added by the other peer, we register a listener on the local  `RTCPeerConnection`  listening for the  `track`  event. ==The  `RTCTrackEvent`  contains an array of  `MediaStream`  objects that have the same  `MediaStream.id`  values as the peer's corresponding local streams. In our example, each track is only associated with a single stream.==
			- Note that while  `MediaStream`  IDs match on both sides of the peer connection, the same is generally not true for  `MediaStreamTrack`  IDs.
			- ```js
			  const remoteVideo = document.querySelector('#remoteVideo');
			  
			  peerConnection.addEventListener('track', async (event) => {
			      const [remoteStream] = event.streams;
			      remoteVideo.srcObject = remoteStream;
			  });
			  ```
		-
	- [Data channels  |  WebRTC](https://webrtc.org/getting-started/data-channels)
	  id:: 66154b5b-efe5-48c4-8c9c-9024d3631841
	  collapsed:: true
		- The WebRTC standard also covers an API for sending arbitrary (任意的) data over a  `RTCPeerConnection` . This is done by calling  `createDataChannel()`  on a  `RTCPeerConnection`  object, which returns a  `RTCDataChannel`  object.
		- ```js
		  const peerConnection = new RTCPeerConnection(configuration);
		  const dataChannel = peerConnection.createDataChannel();
		  ```
		- The remote peer can receive data channels by listening for the  `datachannel`  event on the  `RTCPeerConnection`  object. The received event is of the type  `RTCDataChannelEvent`  and contains a  `channel`  property that represents the  `RTCDataChannel`  connected between the peers.
		- ```js
		  const peerConnection = new RTCPeerConnection(configuration);
		  peerConnection.addEventListener('datachannel', event => {
		      const dataChannel = event.channel;
		  });
		  ```
		- ## Open and close events
		  collapsed:: true
			- Before a data channel can be used for sending data, the client needs to wait until it has been opened. This is done by listening to the  `open`  event. Likewise, there is a  `close`  event for when either side closes the channel.
			- ```js
			  const messageBox = document.querySelector('#messageBox');
			  const sendButton = document.querySelector('#sendButton');
			  const peerConnection = new RTCPeerConnection(configuration);
			  const dataChannel = peerConnection.createDataChannel();
			  
			  // Enable textarea and button when opened
			  dataChannel.addEventListener('open', event => {
			      messageBox.disabled = false;
			      messageBox.focus();
			      sendButton.disabled = false;
			  });
			  
			  // Disable input when closed
			  dataChannel.addEventListener('close', event => {
			      messageBox.disabled = false;
			      sendButton.disabled = false;
			  });
			  ```
			- [[#green]]==不知道上面的程序是否写错了, 监听到close事件之后, 不是应该把disabled置为true吗?==
		- ## Messages
		  collapsed:: true
			- Sending a message on a  `RTCDataChannel`  is done by calling the  `send()`  function with the data we want to send. The  `data`  parameter for this function can be either a string, a  `Blob` , an  `ArrayBuffer`  or and  `ArrayBufferView` .
			- ```js
			  const messageBox = document.querySelector('#messageBox');
			  const sendButton = document.querySelector('#sendButton');
			  
			  // Send a simple text message when we click the button
			  sendButton.addEventListener('click', event => {
			    const message = messageBox.textContent;
			    dataChannel.send(message);
			  })
			  ```
			- The remote peer will receive messages sent on a  `RTCDataChannel`  by listening on the  `message`  event.
			- ```js
			  const incomingMessages = document.querySelector('#incomingMessages');
			  
			  const peerConnection = new RTCPeerConnection(configuration);
			  const dataChannel = peerConnection.createDataChannel();
			  
			  // Append new messages to the box of incoming messages
			  dataChannel.addEventListener('message', event => {
			      const message = event.data;
			      incomingMessages.textContent += message + '\n';
			  });
			  ```
		-
	- [TURN server  |  WebRTC](https://webrtc.org/getting-started/turn-server)
	  collapsed:: true
		- For most WebRTC applications to function a server is required for relaying the traffic between peers, since a direct socket is often not possible between the clients (unless they reside on the same local network). The common way to solve this is by using a TURN server. The term stands for Traversal Using Relays around NAT, and it is a protocol for relaying (中继) network traffic.
		- There are currently several options for TURN servers available online, both as self-hosted applications (like the open-source COTURN project) and as cloud provided services.
		- Once you have a TURN server available online, all you need is the correct  `RTCConfiguration`  for your client application to use it. The following code snippet illustrates a sample configuration for a  `RTCPeerConnection`  where the TURN server has the hostname  `my-turn-server.mycompany.com`  and is running on port  `19403` . The configuration object also support the  `username`  and  `credential`  properties for securing the access to the server. These are required when connecting to a TURN server.
		- ```js
		  const iceConfiguration = {
		    iceServers: [
		        {
		            urls: 'turn:my-turn-server.mycompany.com:19403',
		            username: 'optional-username',
		            credential: 'auth-token'
		        }
		    ]
		  }
		  
		  const peerConnection = new RTCPeerConnection(iceConfiguration);
		  ```
	- [Testing WebRTC applications](https://webrtc.org/getting-started/testing)
	  collapsed:: true
		- When writing automated tests for your WebRTC applications, there are useful configurations that can be enabled for browsers that make development and testing easier.
		- ## Chrome
			- When running automated tests on Chrome, the following arguments are useful when launching:
			- `--allow-file-access-from-files`  - Allows API access for file:// URLs
			- `--disable-translate`  - Disables the translation popup
			- `--use-fake-ui-for-media-stream`  - Provide fake media streams. Useful when running on CI servers.
			- `--use-file-for-fake-audio-capture=<filename>`  - Provide a file to use when capturing audio.
			- `--use-file-for-fake-video-capture=<filename>`  - Provide a file to use when capturing video.
			- `--headless`  - Run in headless mode. Useful when running on CI servers.
			- `--mute-audio`  - Mute audio output.
		- ## Firefox
			- When running automated tests on Firefox, we need to provide a set of preference keys that will be used on the launched instance. Below is the configuration used for the WebRTC samples automated tests:
			- ```
			  "prefs": {
			      "browser.cache.disk.enable": false,
			      "browser.cache.disk.capacity": 0,
			      "browser.cache.disk.smart_size.enabled": false,
			      "browser.cache.disk.smart_size.first_run": false,
			      "browser.sessionstore.resume_from_crash": false,
			      "browser.startup.page": 0,
			      "media.navigator.streams.fake": true,
			      "media.navigator.permission.disabled": true,
			      "device.storage.enabled": false,
			      "media.gstreamer.enabled": false,
			      "browser.startup.homepage": "about:blank",
			      "browser.startup.firstrunSkipsHomepage": false,
			      "extensions.update.enabled": false,
			      "app.update.enabled": false,
			      "network.http.use-cache": false,
			      "browser.shell.checkDefaultBrowser": false
			  }
			  ```
	- [Unified Plan SDP format - transition plan  |  WebRTC](https://webrtc.org/getting-started/unified-plan-transition-guide)
	  collapsed:: true
		- Google is planning to transition Chrome’s WebRTC implementation from the current SDP format (called “Plan B”) to a standards conformant format (“Unified Plan”, draft-ietf-rtcweb-jsep) over the next couple of quarters.
		- The plan involves 5 phases, and one transient API feature.
		- ## Who will be affected
			- People who use multiple audio tracks or multiple video tracks on a single PeerConnection will have to test their product under Unified Plan, and adapt accordingly. In the case where a call is initiated from a non-Chrome endpoint and replied to by Chrome, the form of the offer may have to change. People who do detailed SDP parsing and care about msid attributes will have to check that their parsing code picks up the new format (a=msid). The details on whether changes will be needed and how the apps need to change will be application dependent. We think that almost all applications that use only a single audio and a single video track per RTCPeerConnection will be unaffected by the change.
- Codelabs
  collapsed:: true
	- [Firebase + WebRTC Codelab](https://webrtc.org/getting-started/firebase-rtc-codelab)
		- ## 1. Introduction
		  collapsed:: true
			- In this codelab, you'll learn how to build a simple video chat application using the WebRTC API in your browser and Cloud Firestore for signaling. The application is called FirebaseRTC and works as a simple example that will teach you the basics of building WebRTC enabled applications.
			- > **Note:** Another option for signaling could be Firebase Cloud Messaging. However, that is currently only supported in Chrome and in this codelab we will focus on a solution that works across all browsers supporting WebRTC.
			- ### What you'll learn
				- Initiating a video call in a web application using WebRTC
				- Signaling to the remote party (远程方) using Cloud Firestore
			- ### What you'll need
				- Before starting this codelab, make sure that you've installed:
					- npm which typically comes with Node.js - Node LTS is recommended
		- ## 2. Create and set up a Firebase project
			- ### Create a Firebase project
				- [In the Firebase console](https://console.firebase.google.com/), click Add project, then name the Firebase project FirebaseRTC.
				- Remember the Project ID for your Firebase project.
				- > **Note:** If you go to the home page of your project, you can see it in Settings > Project Settings (or look at the URL!)
				-
-