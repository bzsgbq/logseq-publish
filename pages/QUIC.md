- ## MyNotes
	- 主要
		- ((6617478b-51d2-4971-bbb3-0e4a2c8290f2))
		- ((66174a11-c31e-4a3c-83b2-9a0eb267c29d))
		- ((6617881e-6475-4ca7-8e8a-a5f6b2fd941f))
	- 其它
		- ((6617e028-cc4a-4144-9a88-6a70f43cba92))
		- ((6617e030-fbec-49cb-8676-a5a563efc522))
-
- [QUIC核心原理和握手过程_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Mg411s7mP/?spm_id_from=333.337.search-card.all.click&vd_source=599fbcc1d46a99910a3f21226efa36a4)
  collapsed:: true
	- HTTP2.0 vs HTTP3.0
	  collapsed:: true
		- ![image.png](../assets/image_1712838851168_0.png){:width 400,:height 800}
	- 理论上解决队头阻塞问题
	  collapsed:: true
		- ![image.png](../assets/image_1712839044430_0.png){:width 400,:height 800}
	- 0-RTT
	  collapsed:: true
		- 大概图示
		  collapsed:: true
			- ![image.png](../assets/image_1712839193185_0.png){:width 400,:height 800}
			- ![image.png](../assets/image_1712839234615_0.png){:width 400,:height 800}
		- 详细过程
		  collapsed:: true
			- ![image.png](../assets/image_1712839911034_0.png){:width 400,:height 800}
				- C --> Initial Handshake: Client Hello --> S
				  collapsed:: true
					- 不仅仅是 "说Hello" 这么简单, 初始化握手的数据包里, 就有加密前需要提供的信息了; 所以在Client Hello和Server Hello两个数据包之后, 就是加密的数据包了
				- C <-- Initial Handshake: Server Hello <-- S
				- C <-- 证书, 加密扩展 <-- S
				- C --> FINISH --> S
				- C <-- HANDSHAKE_DONE <-- S
				- NOTE: Server Hello和证书等可以一起发送的, 而之后的FINISH和HANDSHAKE_DONE也可以和实际的数据包一起发送, 所以建立连接其实只需要1-RTT (前三个数据包)
			- ![image.png](../assets/image_1712840384355_0.png){:width 400,:height 800}
			- QUIC中内置的TLS1.3相比于HTTP2.0下层的TLS1.3, 加密了更多信息
				- ![image.png](../assets/image_1712840477363_0.png){:width 400,:height 800}
				-
	- 连接迁移
	  collapsed:: true
		- TCP使用四元组标识一个连接, 一旦从WiFi切换到4G或者5G网络, IP地址发生变化, 就要重新建立连接
		- QUIC包中有 Connection ID 字段, 可以唯一标识一个连接, 实现更丝滑的连接迁移
	- 流量控制
	  id:: 6617e028-cc4a-4144-9a88-6a70f43cba92
	  collapsed:: true
		- 在Client Hello数据包中, 会定义一些quic_transport_parameters, 用来进行流量控制
			- ![image.png](../assets/image_1712840810742_0.png){:width 777}
	- 重传
	  id:: 6617e030-fbec-49cb-8676-a5a563efc522
	  collapsed:: true
		- 通过ACK帧的内容, 告知对方什么收到了, 什么需要重传
	-
- [What is QUIC? Everything You Need to Know | Auvik](https://www.auvik.com/franklyit/blog/what-is-quic-protocol/)
  collapsed:: true
	- ## What is the QUIC protocol?
		- 基于UDP
			- 0-RTT (更快的连接建立)
			  id:: 6617478b-51d2-4971-bbb3-0e4a2c8290f2
			  collapsed:: true
				- QUIC最开始发送的两个数据包Client Hello和Server Hello中, 会同时携带==连接信息==和==TLS信息==, 所以仅需要两次握手, 1-RTT就可以完成==连接建立==和==密钥协商==, 之后就可以发送加密后的实际数据
				- 之后再连接的时候，应用数据包可以和 QUIC 握手信息（==连接信息== + ==TLS 信息==）一起发送，达到 0-RTT 的效果。
				- trivial
				  collapsed:: true
					- **QUIC 内部包含了 TLS，它在自己的帧会携带 TLS 里的“记录”，再加上 QUIC 使用的是 TLS 1.3，因此仅需 1 个 RTT 就可以「同时」完成==建立连接==与==密钥协商==，甚至在第二次连接的时候，应用数据包可以和 QUIC 握手信息（==连接信息== + ==TLS 信息==）一起发送，达到 0-RTT 的效果**。
						- 建立连接: 确认双方的 "连接ID"
					- TCP
						- TCP + TLS1.2 : 三次握手(1RTT) + 四次握手(2RTT) = 3RTT
						- TCP + TLS1.3 : 三次握手 (1RTT) + 两次握手 (1RTT) = 2RTT
					- QUIC
						- initial hello
						- certificate
					- In one word, ==the motivation behind the development of QUIC is **speed**==. In contrast to HTTPS leveraging TLS, which is built on top of the TCP protocol, QUIC is built on top of UDP. This comes with one clear advantage: the time to the first valuable communication drops significantly.
					- Since QUIC uses UDP, there is no need to complete a complex handshake to establish the first connection. The protocol includes initiating encryption, and the exchange of keys, in the initial handshake process. It takes only one round trip to establish a path for communication.
					  (QUIC包括在初始握手过程中**启动加密**和**交换密钥**。建立通信路径只需一次往返。)
					- ![image.png](../assets/image_1712801585972_0.png){:width 600}
			- 不可靠
			  collapsed:: true
				- 需要解决丢包问题
					- And while UDP itself is a connectionless protocol, and therefore technically unreliable (meaning that packets can get lost), QUIC handles identifying lost data and completing re-transmissions to ensure a seamless user experience.
					- ((6617e030-fbec-49cb-8676-a5a563efc522))
	- ## What’s QUIC used for?
		- trivial
		  collapsed:: true
			- The need for a protocol like QUIC is obvious, at least in hindsight. When TCP, HTTP, and SSL / TLS became the standards for web traffic, they were the best protocols available at that time. They were, however, all built independently of each other. Meaning that HTTP was built to use TCP, and operated independently. TCP doesn’t care what type of traffic it carries.
			- This is a great model to create a stack of independent protocols that can each evolve and improve at their own pace, but it does create inefficiencies. In the example we discussed previously, TCP must complete its three-way handshake before TLS can start establishing encryption parameters. Not so efficient.
			- By examining the logical path that data must flow down the OSI model, developers were able to identify some of these inefficiencies and optimize QUIC to do one thing really well. And the timing on this couldn’t be better (or rather, what’s taken so long to define this as a standard!) as the amount of web traffic is ever-increasing.
			- The adoption of QUIC has been rising. At the time of writing this post (late 2021), only about 5.9% of websites support QUIC. However, some big names have really gotten behind it. The majority of Google’s web traffic is now QUIC. Facebook has also jumped in, claiming that over 75% of their traffic is along the new protocol.
		- **multiplexing** (多路复用) (解决队头阻塞问题)
		  id:: 66174a11-c31e-4a3c-83b2-9a0eb267c29d
			- 虽然HTTP/2中的多路复用允许多个流在一个TCP连接上传输, 但是由于TCP需要顺序确认, 所以依然是一个串行的过程, 其中一个流中出现丢包, 所有流都会被阻塞 (Stream 可以认为就是一条 HTTP 请求)
			- QUIC中的多路复用, 由于它所基于UDP不会关心丢包问题, 所以各个流可以独立传输, 某个流发生丢包，只会影响该流，而不会影响其他流。
				- QUIC 中每个数据包都有一个序号唯一标识, 可以据此得到哪些数据包需要重传
				- 可以定义一些重传参数, 通过ACK帧进行发送, 告诉对方哪些包接收到了, 哪些包需要重传
			- Why have these companies gone all in? It goes beyond just the benefits we’ve discussed so far on initial latency. [[#red]]==There’s also a benefit of **multiplexing**.== ==While the HTTP/2 protocol does allow multiple HTTP streams to share a single TCP connection, there is still a problem with packet loss. Since TCP is a connection-oriented protocol and needs to recover the lost data, any packet loss on any of the multiplexed streams would lead to *all* streams on that connection becoming blocked until that loss is recovered. It’s like being on a highway, and one person tapping the brakes in a single lane, and *all* lanes come to a halt. (这就像在高速公路上，一个人在单车道上轻踩刹车，所有车道都会停下来。)==
			- ==QUIC resolves this issue by enabling each lane to keep running independently.== UDP is indifferent to the lost packets. The protocol has defined the retransmission parameters, and enables the retransmission of data in just one stream, as opposed to blocking all streams. So there’s no slowing down in other lanes.
			- ![image.png](../assets/image_1712802221619_0.png){:width 400,:height 800}
		- **connection migration** (连接迁移)
		  id:: 6617881e-6475-4ca7-8e8a-a5f6b2fd941f
		  collapsed:: true
			- HTTP/2 基于 TCP, 而TCP是通过四元组（源 IP、源端口、目的 IP、目的端口）标识一个连接的
			- 那么当移动设备的网络从 4G 切换到 WiFi 时，意味着 IP 地址变化了，那么就必须要断开连接，然后重新建立连接，而建立连接的过程包含
				- TCP 三次握手和 TLS 四次握手的时延
				- 拥塞窗口慢启动的减速过程
			- 给用户的感觉就是网络突然卡顿了一下，因此连接的迁移成本是很高的。
			- 而 QUIC 协议没有用四元组的方式来“绑定”连接，而是通过**连接 ID** 来标记通信的两个端点，客户端和服务器可以各自选择一组 ID 来标记自己，因此即使移动设备的网络变化后，导致 IP 地址变化了，只要仍保有连接信息和TLS信息（比如连接 ID、TLS 密钥等），就可以“无缝”地复用原连接，消除重连的成本，没有丝毫卡顿感，达到了**连接迁移**的功能。
			- There are a few other benefits, but the last key one I’ll touch on is the portability of a session between connections, or connection migration. Portable devices are ubiquitous now, so it’s quite often that you’ll start a session on your mobile device on your home Wi-Fi network, and then head out into the world and the phone will switch over to the cellular 4G or 5G network. QUIC has been designed to handle these changes in a client IP address, leveraging a unique connection ID for that session.
-
- ## Some Questions about QUIC
  collapsed:: true
	- ### QUIC 协议为什么选择 UDP
		- 那么可能就会有人考虑到去修改 TCP 协议，其实这已经是一件不可能完成的任务了。因为 TCP 存在的时间实在太长，已经充斥在各种设备中，并且这个协议是由操作系统实现的，更新起来不大现实。