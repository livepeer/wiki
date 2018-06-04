# Livepeer Whitepaper

**分布式视频流媒体传输协议 及 经济激励**

**Protocol and Economic Incentives For a Decentralized Live Video Streaming Network**

Doug Petkanics <doug@livepeer.org> <br>
Eric Tang <eric@livepeer.org> <br>

翻译 <br>
Elnino Wang <elninowang@qq.com>

## 摘要 ###########################################

The Livepeer project aims to deliver a live video streaming network protocol that is fully decentralized, highly scalable, crypto token incentivized, and results in a solution which can serve as the live media layer in the decentralized development (web3) stack. In addition, Livepeer is meant to provide an economically efficient alternative to centralized broadcasting solutions for any existing broadcaster. In this document we describe the Livepeer Protocol - a delegated stake based protocol for incentivizing participants in a live video broadcast network in a game-theoretically secure way. We present solutions for the scalable verification of decentralized work, as well as the prevention of useless work in an attempt to game the token allocations in an inflationary system.

Livepeer项目旨在提供实时视频流媒体网络协议，该协议完全去中心化，高度可扩展，加密Token被激励，并产生可用作去中心化式开发（web3）堆栈中的实时媒体层的解决方案。 此外，Livepeer旨在为任何现有直播发布者的集中直播解决方案提供经济高效的替代方案。 在本文中，我们描述了Livepeer协议 - 一种基于赌注的委托协议，用于以理论上安全的方式激励现场视频直播网络中的参与者。 我们提出了去中心化工作的可扩展验证解决方案，以及防止无用工作，试图在通货膨胀系统中进行Token分配。

Livepeer项目旨在提供一种完全去中心化、高度可扩展、加密Token激励的实时视频流网络协议，并产生一种解决方案，该解决方案可以作为去中心化式开发（Web3）堆栈中的实况媒体层。此外，LIVEPER旨在为任何现有的直播提供一种经济高效的集中直播解决方案。在本文中，我们描述了LIVEPELL协议——基于博弈的理论上安全的方法，用于授权直播视频直播网络中的参与者。我们提出的解决方案，去中心化工作的可扩展性验证，以及防止无用的工作，试图在通货膨胀系统中的Token分配游戏。

## 目录 ###########################################

<!-- TOC -->

- [Livepeer Whitepaper](#livepeer-whitepaper)
    - [摘要](#摘要)
    - [目录](#目录)
    - [介绍和背景](#介绍和背景)
        - [The Live Video Stack 直播视频栈](#the-live-video-stack-直播视频栈)
    - [Livepeer 协议](#livepeer-协议)
        - [Video Segments 视频片段](#video-segments-视频片段)
        - [Livepeer Token](#livepeer-token)
        - [Protocol Roles 协议角色](#protocol-roles-协议角色)
        - [Consensus 共识](#consensus-共识)
        - [Bonding + Delegation 绑定+授权](#bonding--delegation-绑定授权)
        - [Transcoder() Transaction 事务](#transcoder-transaction-事务)
        - [Broadcast + Transcoding Job 直播+转码作业](#broadcast--transcoding-job-直播转码作业)
            - [Preprocessing 预处理](#preprocessing-预处理)
            - [The Job 作业](#the-job-作业)
            - [End Job 完成工作](#end-job-完成工作)
        - [Verification of Work 工作的验证](#verification-of-work-工作的验证)
            - [A Note On Truebit 关于TruteBIT的备注](#a-note-on-truebit-关于trutebit的备注)
        - [Token Generation  Token的生成](#token-generation--token的生成)
        - [Slashing 削弱](#slashing-削弱)
        - [Token Distribution Token的分配](#token-distribution-token的分配)
        - [Governance 治理](#governance-治理)
    - [Attacks 攻击](#attacks-攻击)
        - [Consensus Attacks 共识攻击](#consensus-attacks-共识攻击)
        - [DDoS](#ddos)
        - [Useless or Self Dealing Transcoder 无用的或者自消耗的转码器](#useless-or-self-dealing-transcoder-无用的或者自消耗的转码器)
        - [Transcoder Griefing   转码器 Griefing](#transcoder-griefing---转码器-griefing)
        - [Chain Reorg 链重组](#chain-reorg-链重组)
    - [Live Video Distribution 直播视频分发](#live-video-distribution-直播视频分发)
    - [Use Cases 使用案例](#use-cases-使用案例)
        - [Pay-As-You-Go Content Consumption 对内容消费即付即用](#pay-as-you-go-content-consumption-对内容消费即付即用)
        - [Auto-scaling Social Video Services 自动缩放社交视频服务](#auto-scaling-social-video-services-自动缩放社交视频服务)
        - [Uncensorable Live Journalism 不能现场直播的新闻业](#uncensorable-live-journalism-不能现场直播的新闻业)
        - [Video Enabled DApps 启动视频的DApps](#video-enabled-dapps-启动视频的dapps)
    - [Summary 总结](#summary-总结)
    - [Appendix 附录](#appendix-附录)
        - [Livepeer Protocol Parameter Reference  Livepeer 协议参数参考](#livepeer-protocol-parameter-reference--livepeer-协议参数参考)
        - [Livepeer Protocol Transaction Types Livepeer协议事务类型](#livepeer-protocol-transaction-types-livepeer协议事务类型)
    - [References 参考](#references-参考)

<!-- /TOC -->

*Note: This document will continue to be updated along the path to the 1.0 deployment of Livepeer. Check back for changes.*

*注意：本文档将继续沿着1.0部署Livepeer的路径进行更新。 检查更改。*

## 介绍和背景 ###########################################

The vision of the decentralized web has begun to be realized over the past couple years with the emergence of networks like [Ethereum](http://ethereum.org) to enable trustless computing, [Swarm](http://swarm-gateways.net/bzz:/theswarm.eth/) and [IPFS/Filecoin](http://ipfs.io) to enable decentralized storage and content distribution, Bitcoin and various token projects to facilitate p2p transfer of value, and decentralized name registries like [Blockstack](http://blockstack.org) and [ENS](http://ens.readthedocs.io/en/latest/introduction.html) to provide human accessible names to content and identities. These elements form the basis for decentralized applications (DApps) to be built in the form of largely static or infrequently updated web or mobile content, but at the moment DApps still lack the ability to include streaming media and data in an open and decentralized way. The goal of the Livepeer project is to decentralize live video broadcast over the internet.

在过去的几年中，去中心化网络的愿景已经开始实现，随着[Ethereum](http://ethereum.org)等网络的出现，使得计算是可信任的，[Swarm](http://swarm-gateways.net/bzz:/theswarm.eth/) 和  [IPFS/Filecoin](http://ipfs.io)，以实现去中心化的存储和内容分发，比特币和各种代币项目以促进p2p价值转移，以及去中心化的名称注册管理机构 如[Blockstack](http://blockstack.org)和[ENS](http://ens.readthedocs.io/en/latest/introduction.html) 为内容和身份提供人性化的名称。 这些元素构成了分布式应用程序(DApps)的基础，以大部分静态或不常更新的Web或移动内容的形式构建，但目前DApps仍然缺乏以开放和去中心化的方式包含流媒体和数据的能力。 Livepeer项目的目标是通过互联网去中心化视频直播。

The [Livepeer Project Overview](https://github.com/livepeer/wiki/wiki/Project-Overview) provides a nice introduction to the current state of live video on the internet. This whitepaper will largely focus on the cryptoeconomic protocol details of Livepeer, rather than the business case, but in summary the overview describes the current state of live streaming as growing at a rapid pace, centralized, and expensive. On the other hand, a fully decentralized P2P solution, where nodes contributed their own computation and bandwidth in service of streaming live video would be more open and scalable, as there would be no limit to the number of connections that could be served.

Livepeer项目概述](https://github.com/livepeer/wiki/wiki/Project-Overview) 提供了一个很好的介绍当前视频在互联网上的状态。该白皮书将主要集中于Live对等的加密经济协议细节，而不是业务案例，但总而言之，该概述描述了以快速、集中和昂贵的方式增长的直播流的当前状态。另一方面，完全去中心化的P2P解决方案，其中节点贡献自己的计算和带宽的流式直播视频服务将更加开放和可扩展性，因为将没有限制的数量，可以提供的连接。

This technology is certainly available to a certain extent, but to date there has been no incentive to get users to run nodes that provide this functionality, nor has there been proper funding for the development of an open protocol that can facilitate this in a way that benefits the entire internet rather than one centralized company. However, with the recent emergence of crypto token powered protocols [[2, 3](#references)], there is now an opportunity to simultaneously incentivize users to contribute computation and bandwidth towards live video broadcast, in a way that aligns with financing the development of an open media server solution capable of delivering live streamed video according to all the latest standards and formats required to reach the full range of devices. Additionally, the economic actions traditionally seen as a result of token powered protocols indicate that the cost to the broadcaster in order to use the Livepeer network could be cheaper than the cost of any centralized solution.

当然，这种技术在一定程度上是可用的，但到目前为止，还没有激励用户运行提供此功能的节点，也没有合适的资金来开发开放协议，这可以有利于整个因特网的发展。而不是一个集中的公司。然而，随着最近出现的密码Token供电协议 [[2, 3](#references)]，现在有机会同时激励用户向实时视频直播贡献计算和带宽，这与资助开放媒体服务的发展相一致。ER解决方案能够根据所有最新的标准和格式，以达到全范围的设备提供实时流视频。此外，传统上认为是Token供电协议的经济行为表明，为了使用Livepeer网络，直播发布者的成本可能比任何集中式解决方案的成本便宜。

As the Livepeer technology and protocol are delivered, it will enable users to participate in the following flow:

随着LIVER对等技术和协议的交付，它将使用户能够参与以下流程：

1. Capture a video on your camera, phone, screen, or web cam and send it into the Livepeer network.
2. Nodes running within the network will encode it into all the necessary formats to reach every supported device. Users running these nodes will be incentivized via fees paid by the broadcaster in ETH, and the opportunity to build reputation through the protocol token to earn the right to perform more work in the future.
3. Any user on the network can request to view the stream, and it will automatically be distributed to them in near realtime.

>

1. 在相机、电话、屏幕或网络摄像头上捕获视频并将其发送到Livepeer网络。
1. 在网络中运行的节点将把它编码成所有必需的格式，以达到每一个支持的设备。运行这些节点的用户将通过ETH广播支付的费用来激励，并有机会通过协议Token来建立声誉，以赚取将来执行更多工作的权利。
1. 网络上的任何用户都可以请求查看流，并且它将在近实时地自动分发给它们。


<img src="https://s3.amazonaws.com/livepeerorg/LPExample.png" alt="Livepeer Network Example" style="width: 750px">

### The Live Video Stack 直播视频栈

The technology stack for broadcasting live video has evolved over many years and contains many layers. Broadcasters need to capture video at the source, interface with a media server to process and transcode the video into many different formats, distribute the video across a network, and then allow the video to be played in high perceived quality by the end consumer. There are also economic questions that are introduced when one thinks through this stack, such as whether it should be the broadcaster or consumer who should be paying for the bandwidth to transfer the video.

直播视频的技术栈已经发展了多年，包含了很多层。直播发布者需要在视频源处捕获视频，与媒体服务器接口，以处理和将视频转换成多种不同的格式，通过网络分发视频，然后允许终端消费者以高感知质量播放视频。当人们思考这个堆栈时，也会出现一些经济问题，比如应该是直播发布者还是消费者，他们应该支付带宽来传输视频。

A typical live streaming platform today needs to support RTMP, HLS, Mpeg-Dash video formats in H.264 and VP8 codec. New codecs like H.265/HEVC, VP9, and AV1 will become more popular in the near future as consumers become more accustomed to higher video quality.  For HLS alone, [Apple suggests](https://developer.apple.com/library/content/documentation/General/Reference/HLSAuthoringSpec/Requirements.html#//apple_ref/doc/uid/TP40016596-CH2-SW1) bitrates from 145kb/s all the way up to 7800kb/s, in order to serve the different types of devices under different conditions. All of this adds a significant amount of complexity and cost to live video broadcasting.

一个典型的实时流媒体平台需要支持H.264和VP8编解码器中的RTMP、HLS、MPEG DASH视频格式。新的编解码器如H.265/HEVC、VP9和AV1将在不久的将来变得更加流行，因为消费者变得更习惯于更高的视频质量。对于单独的HLS，[苹果建议](https://developer.apple.com/library/content/documentation/General/Reference/HLSAuthoringSpec/Requirements.html#//apple_ref/doc/uid/TP40016596-CH2-SW1) 比特率从145kb/s一直到7800 kb/s，以便服务于不同条件下不同类型的设备。条件。所有这些都为直播视频增加了大量的复杂性和成本。

The existing decentralized development stack (web3) contains solutions for some of the layers required for a live video platform, like file transfer and payments, but currently there are no solutions for the capture and interface, transcoding and processing, and serving layers of live video. For this, Livepeer introduces the [Livepeer Media Server (LPMS)](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server) - an open source implementation of a media server which provides all of the live video specific functionality necessary for DApp developers and existing broadcasters to build live functionality into their applications. [Read more about it here](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server).

现有的去中心化开发堆栈（Web3）包含了对直播视频平台所需的一些层的解决方案，如文件传输和支付，但目前还没有解决方案，用于捕获和接口、转码和处理，以及现场视频的服务层。为此，Livepeer 介绍了 [Livepeer Media Server (LPMS)](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server) —— 一个媒体服务器的开源实现，它提供了DAPP开发者和现有直播发布者所需的所有直播视频特定功能。将活的功能应用到他们的应用程序中。[在这里阅读更多关于它](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server)。

As a standalone application, any developer could build a live application on top of the LPMS, but it would still be centralized and would need to be scaled through traditional means. However when every node on the Livepeer network is running the LPMS, and the protocol’s economic incentives ensure that those nodes will contribute their processing power and bandwidth in service of transcoding and distributing live video, **a self-scaling, pay-as-you-go network is made available to developers, who can simply send their live stream into the network, and have the implementation details of scaling, payment, and media hosting abstracted away**.

作为一个独立的应用程序，任何开发人员都可以在LPMS之上构建一个实时应用程序，但是它仍然是集中式的，并且需要通过传统方式进行缩放。然而，当Livepeer的每个节点运行LPMS时，协议的经济激励确保这些节点将贡献其处理能力和带宽，在转码和分发实时视频的服务中，**自缩放，现收现付网络。对于开发人员来说，他们可以简单地将他们的直播流发送到网络中，并将缩放，付款和媒体托管的实施细节提取出去即可**。

## Livepeer 协议 ###########################################

The Livepeer Protocol defines how the various actors in a live streaming ecosystem participate in a secure and economically rational way. The two major areas that the protocol needs to address are the actual distribution of live video from the source to a large number of consumers in a performant and scalable way, and the economic incentives for encouraging participation in the network in a secure and game-theoretic manner. While this whitepaper will touch on the live video distribution itself where overlapping with the economic protocol, it will largely focus on the latter in order to demonstrate security and economic alignment. At the highest level, the protocol is designed to:

Livepeer协议定义了流媒体生态系统中的各种参与者如何以安全和经济合理的方式参与。协议需要解决的两个主要领域是直播视频从源到大量消费者的实际分发，以一种性能和可扩展的方式，以及鼓励以安全和博弈的方式参与网络的经济激励。虽然该白皮书将触及与经济协议重叠的实时视频分发本身，它将主要集中在后者，以证明安全性和经济一致。在最高级别，协议被设计为：

- Allow any node to send a live video into the network, and optionally pay to have it transcoded into various formats and bitrates.
- Allow any node to request the video from the network.
- Allow participants to contribute their processing power and bandwidth in service of transcoding and distribution of video, and to be compensated accordingly.
>
- 允许任何节点向网络发送直播视频，并可选地付费将其转换成各种格式和比特率。
- 允许任何节点请求来自网络的视频。
- 允许参与者在转码和视频分发服务中贡献他们的处理能力和带宽，并相应地得到补偿。

In a decentralized network where participants are rewarded in proportion to the amount of work that they contributed, the two big challenges that need to be addressed to ensure security are:

在一个区中心的网络中，参与者与他们所贡献的工作量成比例，需要确保安全的两大挑战是：

- Can it be verified that the work that the nodes did was done correctly?
- Are the nodes being awarded for real work that contributed value to the network, as opposed to fake work done in an attempt to gain token allocations unfairly?
>
- 可以验证节点所做的工作是否正确？
- 节点被授予对网络贡献价值的实际工作，而不是为了试图不公平地获得Token分配而伪造的工作？

The Livepeer protocol is designed to address both the verification of work and the prevention of fake work, while also offering solutions for automatic scalability of the network and baked in governance for protocol evolution over time.

Livepeer协议被设计用于解决工作的验证和预防伪造工作，同时也为网络的自动可扩展性提供解决方案，并且随着时间的推移实现协议演进的治理。

### Video Segments 视频片段

The core unit of media within Livepeer is what we will call a `segment`. A segment is a time sliced chunk of multiplexed audio and video of time length `t`. Every segment in the Livepeer network is unique, and contains the cryptographic evidence to verify that the broadcaster intended this specific data for this specific segment. Each stream is made up of many consecutive segments, each containing a sequence number identifying their proper ordering. A segment contains the following fields:

Livepeer中的媒体的核心单元是我们将称为 `segment`”的部分。段是时间长度`t`的多路复用音频和视频的时间切片块。Livepeer网络中的每个段都是唯一的，并且包含加密证据，以验证直播发布者为这个特定的段打算这个特定的数据。每个流由许多连续的段组成，每个段包含一个序列号来标识它们的正确排序。一个段包含以下字段：

| Video Segment Field | Description |
|--------|--------|
| **StreamID** | Identifies the origin node and stream that this segment belongs to. |
| **SequenceNumber** | The sequential order that this segment belongs in the original stream. |
| **DataPayload** | The binary metadata and data representing the audio/video in this segment. |
| **DataHash** | The hash of the data payload. |
| **BroadcasterSignature** | A signature from the broadcaster of `Priv(StreamID, SequenceNumber, hash(StreamID, SequenceNumber, DataHash))` which can be used to attest and verify that the broadcaster claims this to be the true data for this unique segment. |

| 视频段字段 | 描述 |
|--------|--------|
| **StreamID** | 标识此段属于的源节点和流。 |
| **SequenceNumber** | 该段属于原始流的顺序。 |
| **DataPayload** | 表示该段中音频/视频的二进制元数据和数据。 |
| **DataHash** | 数据有效载荷的哈希。 |
| **BroadcasterSignature** | 来自 `Priv(StreamID, SequenceNumber, hash(StreamID, SequenceNumber, DataHash))` 的一个签名，它可以用来证明和验证直播发布者声称这是这个独特片段的真实数据。 |


The Livepeer protocol generally uses segments as the unit of work for transcoding, distribution, and payments.

Livepeer协议通常使用段作为转码、分发和支付的工作单元。

### Livepeer Token

The Livepeer Token (LPT) is the protocol token of the Livepeer network. But it is not the medium of exchange token. Broadcasters use Ethereum's Ether (ETH) to broadcast video on the network. Nodes who contribute processing and bandwidth earn ETH in the form of fees from broadcasters. LPT is a staking token that participants who want to perform work on the network stake in order to coordinate how work gets distributed on the network, and to provide security that the work will get done honestly and correctly. LPT has the following purposes:

Livepeer Token（LPT）是Livepeer网络的协议Token。但它不是交换Token的媒介。直播发布者使用以太币（ETH）在网络上播放视频。贡献处理和带宽的节点从直播发布者的收费形式获得ETH。LPT是一个标记Token，参与者想要在网络上执行工作，以协调工作如何分布在网络上，并提供工作将得到诚实和正确地完成的安全性。LPT有以下目的：

- It serves as a bonding mechanism in a delegated proof of stake system, in which stake is delegated towards transcoders (or validators) who participate in the protocol to transcode video and validate work. The token, and potential slashing that occurs due to protocol violation, is necessary in order to secure the network against a number of attacks. More below.
- It routes work through the network in proportion to the amount of staked and delegated token, essentially serving as a coordination mechanism.
- It is a unit of account that is specific to the Livepeer ecosystem, which forms the basis of a SectorCoin concept, applicable to additional functionality to be introduced in the future [[4](#references)]. Services such as DVR, closed captioning, ad insertion/monetization, and analytics can all plug into the Livepeer ecosystem and potentially make use of the security provided by staking LPT.
>
- 它作为一个结合机制，在一个委派证明的股份系统，其中的股权委托给转码者（或验证者）谁参与协议转码视频和验证工作。Token和由于协议违反而发生的潜在削减是必要的，以确保网络免受多个攻击。下面更多。
- 它通过与赌注和委托Token的数量成比例地通过网络进行工作，本质上是一种协调机制。
- 它是一个特定的账户单位，它形成了一个部门货币概念的基础，适用于未来将要引入的附加功能 [[4](#references)]。诸如DVR、封闭字幕、广告插入/货币化和分析等服务都可以插入到Livepeer生态系统中，并且潜在地利用STP LPT提供的安全性。

An initial allocation of Livepeer Token will be distributed so that stakeholders can fulfill various roles in, and use the network, and then additional token will be issued according to algorithmically programmed issuance over time. See the [Token Distribution](#token-distribution) section.

Livepeer Token的初始分配将被分配，以便利益相关者可以在网络中使用各种角色，然后根据算法编程的发布随着时间的推移发出附加Token。参见[Token 分布](#token-distribution) 部分。

Following the conventions of Ethereum and many popular ERC20 tokens [[16](#references)], LPT will be divisible by 10 ^ 18, with larger denominations such as the LPT itself intended to be used for user level transactions such as staking, and smaller denominations intended to be used for protocol accounting.

遵循以太坊和许多流行的ERC20Token [[16](#references)] 的约定，LPT可以被10 ^ 18整除，更大的面值，比如LPT本身打算用于用户级别的交易， 旨在用于协议会计的较小面值。

### Protocol Roles 协议角色

Before going forward, let’s define the roles in the network so that there is a common vocabulary for discussing the protocol. A Livepeer node is any computer running the Livepeer software.

在继续之前，让我们定义网络中的角色，以便讨论协议有一个共同的词汇表。 Livepeer节点是运行Livepeer软件的任何计算机。

| Node Role | Description |
|--------|-----------|
| **Broadcaster** | Livepeer node publishing the original stream |
| **Transcoder** | Livepeer node performing the job of transcoding the stream into another codec, bitrate, or packaging format. |
| **Relay Node** | Livepeer node participating in the distribution of live video and passing of protocol messages, but not necessarily performing any transcoding. |
| **Consumer** | Livepeer node requesting the stream, likely to view it or serve it through a gateway to their app or DApp’s users. |

| 节点角色 | 描述 |
|--------|-----------|
| **直播发布者** | Livepeer节点发布原始流。 |
| **转码器** | Livepeer节点执行将流转码为另一种编解码器，比特率或打包格式的工作。 |
| **中继节点** | Livepeer节点参与实时视频的分发和协议消息的传递，但不一定执行任何代码转换。 |
| **消费者** | 请求流的Livepeer节点，可能会查看它或通过网关将其提供给他们的应用程序或DApp的用户。 |


In addition to the above roles played by users running Livepeer nodes, the protocol also will refer to the following systems. While we use certain specific systems to make reference to a possible implementation, alternative systems can also be swapped in if they provide similar functionality and cryptoeconomic guarantees:

除了运行Liverpeer对等节点的用户所扮演的上述角色之外，协议还将参考以下系统。虽然我们使用某些特定的系统来引用可能的实现，但如果提供类似的功能和密码经济保证，也可以交换替代系统：

| System Role | Description |
|-------|----------|
| **Swarm** | Content addressed storage platform. Data can be guaranteed to be available there temporarily during the verification process via SWEAR protocol [[7, 12](#references)]. *(Note in this document we refer to Swarm, but other content addressed storage platforms can be substituted if data availability can be guaranteed with high probability).* |
| **Livepeer Smart Contract** | Smart contract running on the Ethereum network [[1](#references)]. |
| **Truebit** | Blackbox verification protocol that guarantees correctness of computation placed on chain (at a hefty cost) [[6](#references)]. (<http://truebit.io>) |

| 系统角色 | 描述 |
|-------|----------|
| **Swarm** | 内容寻址存储平台。在验证过程中，可以通过SWEAR协议[1] [[7, 12](#references)] 暂时保证数据在那里可用。*（本文档中的注释我们指的是群，但是如果数据可用性可以以高概率保证）*，可以替代其他内容寻址的存储平台。 |
| **Livepeer 智能合约** | 基于以太坊网络的智能合约 [[1](#references)]. |
| **Truebit** |黑盒验证协议，保证了计算链正确性（以高昂的代价） [[6](#references)]. (<http://truebit.io>) |

Here is a visual overview of the roles, and the ways in which they communicate with one another in the work verification process described below.

下面是角色的可视化概述，以及它们在下面描述的工作验证过程中相互通信的方式。

<img src="https://livepeer-dev.s3.amazonaws.com/docs/lpprotocol.png" alt="Protocol Visual Overview" style="width: 750px">  

*Segments flowing from the broadcaster to the transcoder and eventually to the consumer. The transcoder ensures they have signatures and proof of work to participate in the work verification procedure.*

*从直播发布者到转码器的部分，最终流向消费者。转码器确保他们有签名和证明工作参与工作验证程序。

**Note on Transcoders:** Transcoders play the most critical role in the Livepeer ecosystem. They are the ones who are taking an input stream and converting it into many different formats in a timely manner for low latency distribution. As such they benefit from high availability, efficient, powerful hardware (potentially with GPU accelerated transcoding), high bandwidth connections, and solid DevOps practices. Transcoders should churn far less than other network participants, as when they take on the job of transcoding a stream, it’s less than ideal if they drop off the network. While the network can scale to support many participants playing the role of transcoder (and earning the requisite token allocations), this is a special role that’s delegated from most network participants, in order to ensure that a reliable network that provides value to broadcasters is maintained. More below on this delegation.

**关于转码器的注释：** 转码器在Livepeer生态系统中扮演最关键的角色。他们是谁采取的输入流，并将其转换成许多不同的格式，及时为低延迟分布。因此，它们受益于高可用性、高效、强大的硬件（潜在地具有GPU加速的转码）、高带宽连接和坚实的DevOps实践。转码器应该比其他网络参与者少得多，因为当他们从事对流进行转码的工作时，如果它们掉在网络上就不太理想了。虽然网络可以缩放以支持许多参与者扮演代码转码器的角色（并且赚取必要的Token分配），但这是从大多数网络参与者委派的特殊角色，以确保为直播提供价值的可靠网络是维持的。更多关于这个代表团。

### Consensus 共识

Livepeer has a two layer consensus system. The LPT ledger and transactions are secured by the underlying blockchain, such as Ethereum. Any transfer of the LPT token or any transaction in the system can be considered to have been confirmed with the same security as the underlying proof of work or proof of stake blockchain. The second layer however, dictates the distribution of newly generated LPT. This is governed by the Livepeer Smart Contract, and participation in the protocol by various actors. While there is no consensus required per say, in terms of acceptance and validation of previous blocks, the protocol defines rules for participation and conditions upon which actors will be penalized (slashed) for failing to fulfill their role.

Livepeer有两层共识系统。LPT分类帐和交易由基础链链（如Ethereum）担保。LPTToken或系统中的任何交易的任何转移都可以被认为是与基础的工作证明或股权链的证明相同的安全性。然而，第二层决定新生成的LPT的分布。这是由Livepeer智能合约管理的，并且由不同的参与者参与协议。虽然没有表示一致的要求，在接受和验证以前的块，该协议定义的参与规则和条件下，演员将受到惩罚（削减）未能履行自己的角色。

This second level of consensus governing the newly generated token is based upon Delegated Proof of Stake (DPOS), as inspired by systems like Bitshares, Steem, Tendermint, and Casper [[5, 9, 10, 11](#references)]. The role of validators in the network is played by Transcoders. Any user can delegate their stake towards a transcoder, who then needs to perform transcoding jobs in the network, participate in the work verification protocol, and invoke functions on chain at specific intervals to validate this work. The protocol will distribute fees and newly generated token, and it will slash the stake of badly behaved actors. The validation result will be recorded on-chain via Truebit after it performs the validation, so there will be no room for disputes between the broadcaster and the transcoder.

第二层次的共识管理新生成的Token是基于委托的股份证明（DPOS），灵感来自于系统，如Bitshares，Steem，Tendermint，和Casper [[5, 9, 10, 11](#references)]。验证器在网络中的角色由转码器来扮演。任何用户都可以将他们的股份委托给代码转码器，后者需要在网络中执行代码转换作业，参与工作验证协议，并在特定的时间间隔调用链上的函数来验证这项工作。该协议将分发费用和新生成的token，并将削减行为不良的角色的股份。验证结果将在TwiteBIT完成验证之后通过Truebit记录在链上，因此直播发布者和转码器之间不会有争执的余地。

### Bonding + Delegation 绑定+授权

In Livepeer, in order to indicate stake in the network, nodes must bond some amount of their LPT. They do this through the `Bond()` transaction, which will tie up their stake in the smart contract until they `Unbond()`, at which point they will enter an unbonding state which will last for `UnbondingPeriod` time. Upon completion of the `UnbondingPeriod` they can then withdraw their LPT.

在Livepeer，为了表示网络中的股份，节点必须结合一定数量的LPT。他们通过`Bond()`交易来完成交易，这将把他们在智能合约中的股份绑在一起，直到他们 `Unbond()`，在这一点上，他们将进入一个非绑定状态，该状态将持续到`UnbondingPeriod`。在完成 `UnbondingPeriod` 之后，他们可以撤回其LPT。

The bonded amount is used to delegate stake towards a Transcoder. The network supports `N` active transcoders at any one time, which is a moveable network parameter. Any node can indicate that it wishes to be a Transcoder with a `Transcoder()` transaction, and the protocol will select the `N` transcoders with the most cumulative stake (their own + delegated from other nodes) at the start of each round, along with one random transcoder from the waitlist.

键合量用于将赌注委托给转码器。该网络在任何时候支持`N`活动转码器，这是一个可移动的网络参数。任何节点都可以指示它希望成为具有 `Transcoder()` 事务的转码器，并且该协议将在每个回合开始时选择具有最多累积的份额（其自身+来自其他节点的委托）的 `N` 转码器，以及来自等待列表的一个随机转码器。

Newly generated token in Livepeer is distributed to bonded nodes in relative proportion to the amount of work that they have bonded (minus fees), as long as they’ve delegated towards transcoding nodes that behave according to the protocol. Bonds can be slashed (reduced by a certain percentage) if the nodes that they’ve delegated towards do not behave and violate one of the slashing conditions. Nodes who have bonded and delegated towards a Transcoder also receive a portion of the fees that the Transcoder generates through transcoding jobs on the network. In essence, nodes who perform work, earn the fees that broadcasters paid for that work.

在Livepeer中，新生成的token与绑定的节点的数量成比例（减去费用），只要它们被委托给根据协议运行的转码节点。如果他们委托的节点不遵守和违反一个削减条件，则可以削减债券（减少一定百分比）。向转码器绑定和委托的节点还接收转码器通过网络上的转码作业生成的部分费用。本质上，执行工作的节点赚取直播发布者为该工作支付的费用。

Going forward, when this document uses the term "delegator", it is referring to bonded nodes who have delegated their stake towards a transcoder candidate, instead of delegating it towards themselves as a transcoder.

展望未来，当该文档使用术语“委托人”时，它指的是绑定节点将他们的股份委托给转码器候选，而不是将其作为转码器委托给他们自己。

In summary, participants choose to bond their stake for the following reasons:

综上所述，参与者选择结合他们的股份,理由如下：

- Participate in delegating towards effective transcoders who will provide great service to the network, ensuring its value to broadcasters.
- Build reputation and future-work allocation in form of allocated token in proportion to stake.
- Earn fees generated from transcoders.
- They may wish to be a Transcoder.

- 参与向有效的转码器的委派，为网络提供巨大的服务，确保其对直播电台的价值。
- 以分配比例与持股比例建立声誉和未来的工作分配。
- 赚取来自转码器生辰的费用。
- 他们可能希望成为转码器。

### Transcoder() Transaction 事务

A node indicates their willingness to be a transcoder by submitting a `Transcoder()` transaction, which publicizes the following three properties:

一个节点通过提交一个`Transcoder()` 事务表明他们愿意成为一个转码器，它将公布以下三个属性：

- `PricePerSegment`: the lowest price they are willing to accept to transcode a segment of video
- `BlockRewardCut`: The % of the block reward that bonded nodes will pay them for the service of transcoding. (Example 2%. If a bonded node were to receive 100 LPT in block reward, then 2 LPT to the transcoder).
- `FeeShare`: The % of the fees from broadcasting jobs that the transcoder is willing to share with the bonded nodes who delegate towards it. (Example 25%. If a transcoder were to receive 100 ETH in fees, they would pay 25 ETH to the bonded nodes).
>
- 'PricePerSegment'：他们愿意接受的最低价格来转码一段视频
- `BlockRewardCut`：保税节点为转码服务支付的块奖励的百分比。 （例2％，如果一个绑定节点在块奖励中接收到100个LPT，则向转码器接收2个LPT）。
- 'FeeShare'：转码器愿意与委托给它的保税节点共享直播作业的费用百分比。 （例如25％。如果一个代码转码器收到100个ETH的费用，他们将向保税节点支付25个ETH）。

The Transcoder can update their availability and information up until `RoundLockAmount` time before the next transcoding round. This is offered as a % of the round. (Example 10% == 2.4 hours. They can change this information until 2.4 hours before the next transcoding round which lasts for `RoundLength` 1 day). This gives bonded nodes the chance to review the fee splits and token reward splits relative to other transcoders, as well as anticipated fees based upon the rate they're charging and network demand, and move their delegated stake if they wish. At the start of a transcoding round (triggered by a call to the `InitializeRound()` transaction), the active transcoders for that round are determined based upon the total stake delegated towards each transcoder, and stakes and rates are locked in for the duration of that round.

转码器可以更新它们的可用性和信息直到下一轮代码转换之前的`RoundLockAmount`时间。这是作为回合的一%。（例10% == 2.4 小时）。他们可以改变这个信息，直到下一个代码转换回合持续1天之前的2.4小时。这使得保税节点有机会审查费用分裂和token奖励分裂相对于其他转码器，以及预期费用的基础上，他们的收费和网络需求，并移动其委派的股份，如果他们希望。在代码转换回合开始时（由对`InitializeRound()`的事务调用）触发，该回合的活动转码器基于委托给每个转码器的总赌注确定，并且在该回合持续期间锁定和速率被锁定。

There is one change that is allowed during the `RoundLockPeriod`: The lowest offered price/segment for any of the candidate transcoders is locked in and can't be moved, but other transcoder candidates can adjust their price/segment downwards. This allows them to match the lowest offered price on the network if they wish in order to guarantee their stake-weighted share of work on the network. They are not allowed to move their offered price upwards during this period.

在`RoundLockPeriod` 期间允许有一个变化：对于任何候选的转码器，最低的提供价格/段被锁定并且不能被移动，但是其他转码器候选可以向下调整它们的价格/段。这使他们能够匹配网络上最低的报价，如果他们希望，以保证他们的股权加权份额的工作在网络上。在这段时间内，他们不允许将报价上调。

Here is an example state of Transcoder options that a delegator can review when deciding whom to delegate towards.

这里是一个转码器选项的示例状态，在决定委托给谁时，委托人可以查看该选项。

| Transcoder ID | PricePerSegment | BlockRewardCut | FeeShare |
|----|----|----|----|
| 1 | 22 wei | 1% | 25% |
| 2 | 30 wei | 2% | 40% |
| 3 | 10 wei | 4% | 1% |
| ... | ... | ... | ... |
| N | 14 wei | 0% | 2% |

*Note on price: In this document we list price/segment. In reality, Livepeer plans to use a gas accounting inspired model where there is a notion of units of gas required for certain job parameters of a segment such as bitrate, encoding, frame size, etc. Price/segment is a stand in, where the incentives are the same, but in reality they’ll likely be communicating price/gas.*

*注意价格：在这份文件中，我们列出价格/段。在现实中，Livepeer计划使用一个gas会计启发模型，其中有一个概念的单位的gas所需的某些工作参数的一个环节，如比特率，编码，帧大小等。价格/段是一个站在那里，激励是相同的，但实际上，他们很可能是沟通中的价格/gas*

### Broadcast + Transcoding Job 直播+转码作业

Transcoders who are open for business on the network, throw their hat into the ring for transcoding work by submitting a `TranscodeAvailability()` transaction. This indicates their availability and places them into a pool of transcoders available to take a newly submitted job.

在网络上开放业务的转码器通过提交一个`TranscodeAvailability()`事务将他们的帽子投入到代码转换工作中。这表明它们的可用性，并将它们放入一个可用新提交的工作的转码器池中。

When a broadcaster submits their stream into the Livepeer network it is given a `StreamID`. This serves as both a unique identifier, and it also contains the origin node address so that nodes know how to request and route requests to consume this stream towards the origin. The stream contains many consecutive `Segments`, as described in the [Video Segments](#video-segments) section. If the broadcaster would like the network to take care of transcoding their stream into all the formats and bitrates necessary to reach every user on every device, then the first step is submitting a transcoding job transaction on chain. Jobs are given a unique ID as well, and the input data to job consists of:

当直播发布者将其流提交到Livepeer网络时，它被赋予`StreamID`。这既充当唯一标识符，又包含源节点地址，以便节点知道如何请求和路由请求，以将该流消耗到原点。该流包含许多连续的`Segments`，如[Video Segments](#video-segments) 中所描述的。如果直播发布者希望网络将其流转换成所有的格式和比特率，以达到每个设备上的每个用户，那么第一步就是提交一个链上的转码作业事务。作业也被赋予唯一的ID，并且作业的输入数据包括：

`Job(StreamID, TranscodingOptions, PricePerSegment)`

The `TranscodingOptions` define the output bitrates, formats, encodings, etc, and the `PricePerSegment` lists the price that the broadcaster will offer.

 `TranscodingOptions` 定义了输出比特率、格式、编码等，并且`PricePerSegment`列出了直播发布者将提供的价格。

As soon as this transaction is mined, the next blockhash will be used to pseudo-randomly determine the transcoder selected for this job. All transcoders with a price that’s lower than or equal to the price offered will be considered, and the blockhash modulus the number of candidate transcoders (weighted by their stakes) will determine the index of the selected transcoder.

一旦该事务被挖掘，下一个块哈希将被用于伪随机地确定为该作业选择的转码器。所有价格低于或等于所提供价格的转码器将被考虑，而块哈希模数的候选转码器（由它们的赌注加权）将决定所选代码转码器的索引。

At this point the broadcaster can begin streaming video segments towards the transcoder, and they’ll participate in the following protocol. The protocol also makes use of a persistent storage solution, for example Swarm, as part of the work verification process.

在这一点上，直播发布者可以开始向转码器流视频片段，并且他们将参与以下协议。该协议还使用持久性存储解决方案，例如Swarm，作为工作验证过程的一部分。

#### Preprocessing 预处理

1.  **Broadcaster**  -> **Livepeer Smart Contract**: submits a deposit on chain to cover the cost of the full transcoding job. This can be refilled later at any point, but the Transcoder may stop work if the deposit runs out as they gradually cash in for work done.

1. **直播发布者** -> **Livepeer智能合约**：在链上提交一个定金以覆盖完整的转码作业的费用。这可以在以后的任何时候重新填写，但是如果存款在完成工作时逐渐用完，转码器就可以停止工作。

#### The Job 作业

2. **Broadcaster** -> **Livepeer Smart Contract**: Job(streamID, options, price/segment)
    - Creates the job request on chain and places some ETH in escrow to pay for the work.
3. The protocol can use the next block hash to deterministically select the correct Transcoder for this job.
4. **Transcoder** -> **Broadcaster**: send output streamID and receipt that the job is accepted.
5. **Broadcaster** -> **Transcoder**: send stream segments, which contain signatures verifying the input data.
7. **Transcoder** performs transcoding and makes new output stream available on network
9. **Transcoder**: Store a transcode receipt for each segment of transcoding work. A transcode receipt has the following fields.
>
2. **关闭者** -> **Livepeer智能合约**：Job(streamID, options, price/segment)
    - 在链上创建作业请求，并在托管中放置一些ETH来支付工作。
3. 该协议可以使用下一个块哈希来确定性地为该作业选择正确的转码器。
4. **转码器**> **播音员**：发送输出流和收据，接受工作。
5. **直播发布者**> **转码器**：发送流段，其中包含验证输入数据的签名。
7. **转码器** 执行转码，并使新的输出流可在网络上使用。
9. **转码器**：为转换工作的每一段存储一个转码收据。转码收据具有以下字段。

| Transcode Receipt Field | Description |
|-------|------------|
| **StreamID** | Identifies the origin node and stream that this segment belongs to. | 
| **Sequence Number** | The sequential order that this segment belongs in the original stream. |
| **Input Data hash** | The hash of the input segment data payload. |
| **Transcoded Data hash** | The hash of the output data after transcoding this segment. |
| **Broadcaster segment signature** | A signature from the broadcaster of Priv(StreamID, Seq#, Dhash) which can be used to attest and verify that the broadcaster claims this to be the true data for this unique segment. |
| **Transcoder segment signature** | A signature of all of the above fields from the transcoder attesting to the claim that this specific output transcoding was performed on this specific input. |

| Transcode Receipt Field | 描述 |
|-------|------------|
| **StreamID** | 标识此段属于的源节点和流。 | 
| **Sequence Number** | 该段属于原始流的顺序。 |
| **Input Data hash** | 输入段数据有效载荷的哈希。 |
| **Transcoded Data hash** | 在对该段进行转码后输出数据的哈希。 |
| **Broadcaster segment signature** | 来自 Priv(StreamID, Seq#, Dhash)的直播发布者的签名，可以用来证明和证实直播发布者声称这是这个独特片段的真实数据。 |
| **Transcoder segment signature** | 来自转码器的所有上述字段的签名，证明该特定输出转码是在该特定输入上执行的。 |

Whenever the transcoder observes that they are no longer receiving segments, they can call `ClaimWork()` to claim their work.

每当转码器观察到它们不再接收片段时，它们可以调用 `ClaimWork()` 来声明它们的工作。
 
#### End Job 完成工作

10. **Transcoder** -> **Livepeer Smart Contract**: Call `ClaimWork(JobID, StartSegmentSeq#, EndSegmentSeq#, MerkleRoot)`. Transcoder is claiming on chain they have performed work on the claimed segment range, with a merkle root of all of the transcode receipt data to commit to the content of these encoded segments.
11. Wait for this transaction to be mined, and observe the next blockhash. The protocol can then determine which segments will be verified based upon the `VerificationRate`.
12. **Transcoder** -> **Swarm**: Write input data payloads for the segments that will be challenged via verification, using SWEAR params to ensure the data will be there long enough for verification (`VerificationPeriod` time).
13. **Transcoder** -> **Livepeer Smart Contract**: Provide transcode claims on chain for each segment that needs to be verified, along with merkle proofs for the receipts for each segment in the transcode claims. The smart contract can verify the signatures from Broadcaster and **Transcoder** to ensure all data necessary is available to conduct verification, and can verify the merkle proofs against the committed merkle root from `ClaimWork()`.
14.  **Transcoder** -> **Truebit**: `Verify()`. This is an onchain call to the Truebit smart contract, where the Transcoder provides the Swarm input hash for the challenged segment. (More on verification in the following section)
15. **Truebit** -> **Livepeer Smart Contract**:  The result of the job is written on chain. This is compared to the transcoding claim result that the Transcoder provided.
16.  **Livepeer Smart Contract**: at this point the Livepeer smart contract has all the information it needs to determine if the Transcoder’s work is verified.
    - If verified correct, then use as input to token allocation algorithm and release of escrowed fees.
    - If incorrect, then Transcoder and its stakers get slashed `FailedVerificationSlashAmount` and the Broadcaster is refunded.
>
10. **转码器** -> **Livepeer智能合约**：调用`ClaimWork(JobID, StartSegmentSeq#, EndSegmentSeq#, MerkleRoot)`。转码器声称在链上他们已经在声明的代码段范围内执行了任务，所有代码转换接收数据的merkle根都提交给这些编码代码段的内容。
11. 等待这笔交易被挖矿，并观察下一个块冲突。协议然后可以根据`VerificationRate`确定哪些段将被验证。
12. **转码器** -> **Swarm**：使用SWEAR参数为将要通过验证挑战的片段写入输入数据有效载荷，以确保数据在那里足够长以进行验证（`VerificationPeriod`时间）。
13. **转码器** -> **Livepeer智能合约**：为需要验证的每个细分市场提供链式转码声明，以及转码声明中每个细分市场收据的merkle证明。智能合同可以验证来自直播发布者和**转码者**的签名以确保所有必要的数据可用于进行验证，并且可以验证来自`ClaimWork()`的承诺的merkle根的merkle证据。
14. **转码器** -> **Truebit**：`Verify()`。这是对Truebit智能合约的链接调用，转码者为挑战的段提供Swarm输入哈希。 （关于下一节中的验证的更多信息）
15. **Truebit** -> **Livepeer智能合约**：工作结果以连锁书写。这与转码者提供的转码声明结果进行比较。
16. **Livepeer智能合约**：此时，Livepeer智能合约拥有确定转码器工作是否得到验证所需的全部信息。
     - 如果验证正确，则用作Token分配算法和释放托管费用的输入。
     - 如果不正确，那么转码器及其代码会削减`FailedVerificationSlashAmount`，直播发布者将退还。

The Broadcaster can stop sending segments at any point, which effectively is an `EndJob()`.

直播发布者可以在任何点停止发送段，这实际上是一个`EndJob()`。

At this point the transcoding has been performed, proof of the work has been claimed on the chain, and failure or success of the verification of the work has been reported. All the info is on chain to determine allocation of fees and token allocations to transcoders and delegators, or slashing in the case of failed verification. Let’s take a look at how work is actually verified.

在这一点上，已经执行了转码，已经在链上声明了工作的证明，并且已经报告了对该工作的验证的失败或成功。所有的信息都在链上，以确定费用分配和token分配给转码器和委托人，或者在失败验证的情况下削减。让我们来看看工作是如何被验证的。

### Verification of Work 工作的验证

In order to allocate fees to transcoders who claim that they have performed a transcoding job, it’s necessary that the protocol can determine that the job was actually performed correctly with high probability. For this, Livepeer extends the research of, and makes use of, the [Truebit Protocol](http://truebit.io) [[6](#references)].

为了向那些声称已经执行转码作业的转码器分配费用，协议有必要确定该作业实际上是以高概率正确执行的。为此，Livepeer扩展了 [Truebit Protocol](http://truebit.io) [[6](#references)]的研究，并使用。

Truebit works by having one participant (the solver) perform the actual work for the fee, in this case transcoding, and then having additional participants (verifiers) verify the work in order to detect mistakes, errors, or cheating. The task is broken down into very small steps, and the verifiers check the work of the solver to find the first step that differs from what they expected it to be. Then, only this one very small step needs to be played out on chain by a smart contract (judge), who can tell which party did the work correctly. The economic incentives, including forced errors to incentivize checking on the part of verifiers, ensure that it is not profitable to cheat or challenge incorrectly, but it is profitable to play the role of checking the work.

Truebit通过让一个参与者（求解器）执行实际的工作来进行收费，在这种情况下，转码，然后有额外的参与者（验证者）验证工作，以检测错误、出错或作弊。任务被分解成非常小的步骤，验证者检查求解器的工作，找到与预期的不同的第一步。然后，只有一个非常小的步骤需要在一个智能合约（裁定）的链条上播放，能知道哪一方正确地完成了工作。经济激励措施，包括强迫错误核查核查人员的一部分，确保不正确地欺骗或挑战是没有利润的，但发挥检查工作的作用是有利可图的。

The downside of this protocol is that it costs between 5x-50x the cost of the original work in order to verify all work. Livepeer uses Truebit as a black box to verify segments, but it gets around having to pay this very high verification tax by only verifying a small percentage of segments randomly, and using slashing in the case of failed verifications. The `VerificationRate` set within Livepeer determines how frequently a specific segment is to be selected for challenge within Truebit, and the randomness of a future block hash after the work has been committed to the blockchain, determines which segments specifically are selected.

该协议的缺点是，为了验证所有的工作，它的成本在原始工作的5X-50X之间。Livepeer使用Truebit作为一个黑箱来验证片段，但是它只需要通过只验证一小部分的片段来支付这个非常高的验证税，并且在失败验证的情况下使用slashing。在Livepeer中设置的`VerificationRate`决定了在Truebit中挑选特定片段进行挑战的频率，以及在工作被提交到区块链之后的未来区块哈西的随机性，确定哪些片段具体被选择。

If work is committed via an `ClaimWork()` call in block `N`, then

如果通过块`N`中的 `ClaimWork()`调用提交工作，那么

If `Sha3(N, BlockHash(N), Seg#) % VerificationRate == 0` then the segment # must be verified.

如果 `Sha3(N, BlockHash(N), Seg#) % VerificationRate == 0` 那么这段 #必须验证。

The Transcoder provides Transcode Claims on chain for the candidate segments by invoking the `Verify()` transaction. The Livepeer Smart Contract can verify the authenticity of these claims using the internal signatures and provided merkle proofs, and then invoke a call to Truebit to verify only these segments.

转码器通过调用`Verify()` 事务为候选段提供链上的转码请求。Livepeer智能合约可以使用内部签名验证这些声明的真实性，并提供梅克尔证据，然后调用Truebit来验证这些片段。

Truebit solvers and verifiers access the input data for a segment from a persistent content addressed storage system, such as Swarm. The Transcoder is responsible for verifying that the segment data is available in Swarm, and can optionally look for receipts from the SWEAR protocol [[5](#references)] guaranteeing persistence for a certain period of time, which is long enough for Truebit to play out. Additionally, they can take it upon themselves to run a Swarm node ensuring that the data is available to Truebit verification. If they have reason to believe that data is not available in Swarm, they can provide it, or just call `ClaimWork()` on the previously available data.

Truebit求解者和验证者从持久的内容寻址存储系统（如Swarm）访问片段的输入数据。 转码器负责验证Swarm中的段数据是否可用，并且可以选择查找SWEAR协议[[5](#references)]中的收据，以确保持续一段时间，这对于Truebit来说足够长来播出。 此外，他们可以自行运行Swarm节点，确保数据可用于Truebit验证。 如果他们有理由相信数据在Swarm中不可用，他们可以提供它，或者只需调用先前可用数据上的 `ClaimWork()` 。

Truebit will write the results of the computation (succeeded or failed) back to the Livepeer Smart Contract, which can then be used in the reward and slashing calculations within the protocol. A transcoding node can not predict in advance which segments will be verified, and the following penalties will be felt in the case of cheating or failing to transcode correctly:

Truebit会将计算结果（成功或失败）写回到Livepeer智能合约中，然后将其用于协议中的奖励和削减计算。 转码节点不能预测哪些段将被验证，并且在作弊或未能正确转码时会感受到以下处罚：

- `FailedVerificationSlashAmount` will be slashed if they fail a verification from Truebit.
- `MissedVerificationSlashAmount` will be slashed if they fail to provide transcode claims and invoke Truebit on segments they were required to do so.
- Lost fee from the broadcaster.
- Not only will the Transcoder be slashed, but all their delegators will be slashed as well. They will take this account into their decision of who to delegate towards, and the Transcoder could lose the lucrative job they hold.
>
- 如果失败了来自Truebit的验证，则`FailedVerificationSlashAmount`将被削减。
- 如果他们未能提供转码声明并且在他们被要求的部分上调用Truebit，则`MissedVerificationSlashAmount`将被削减。
- 从直播发布者收取费用。
- 转码者不仅会被削减，而且他们的所有委托人也会被削减。 他们会将这个帐户纳入他们决定由谁来委派的方式，而转码者可能会失去他们持有的有利可图的工作。

It is important that it be more profitable to simply stake LPT towards a valid, honestly performing transcoder, than it can be to cheat and take slashing penalties while still collecting fees and token allocations for dishonest work. Careful selection of the slashing params and verification rate can ensure this.

很重要的一点是，将LPT与一个有效的、诚实的转码器相比，更有利的是，它可以欺骗和削减惩罚，同时仍然收取不诚实工作的费用和Token分配。仔细选择剪切参数和验证率可以保证这一点。

#### A Note On Truebit 关于TruteBIT的备注

*While the protocol makes use of Truebit in order to provide fully trustless verification of work, it may be necessary in practice to use available solutions that provide verification without the degree of trustlessness that Truebit can offer while Truebit is still under development and testing. Some options, ordered by degree of trustlessness, include:*

*虽然协议使用Truebit，以提供完全可信的验证工作，但在实践中可能需要使用可用的解决方案，提供验证，而Truebit在TetrueBIT仍在开发和测试的情况下可以提供的无可信程度。一些选项，按无可信程度排序，包括：*

*1. Livepeer API Based Oracle - Trust Livepeer to verify computation. Very centralized, not ideal for anything beyond testing.*  
*2. Oraclize Computation Service - Trust a company who provides proofs of computation and who's entire reputation relies upon putting external data on chain with proofs that it wasn't tampered with.*  
*3. Secure hardware enclaves - Services like Intel SGX or TownCrier provide trusted computing environments. Trust that their hardware implementation is correct and secure. This can be decentralized and audited.*

* 1。基于Livepeer API的Oracle - 信任对Livepeer计算进行验证。非常集中，对于测试之外的任何东西都不理想。
* 2。Oraclize计算服务 - 信任提供计算证明的公司和谁的整个声誉依赖于外部数据链上的证据，它没有被篡改。
* 3。安全硬件包 - 英特尔SGX或TownCrier等服务提供可信计算环境。相信他们的硬件实现是正确的和安全的。这可以去中心化和审核。

### Token Generation  Token的生成

Livepeer is inflationary in that new tokens will be generated and allocated over time according to the schedule communicated below in [Token Distribution](#token-distribution). If all roles in Livepeer behave according to the protocol, then newly generated tokens will be allocated to users in proportion to their bonded stake (minus fees). Transcoders have the role of calling the `Reward()` function in order to trigger the new token allocation or slashing which can be computed from all data available on chain.

Livepeer是通货膨胀的，新的Token将随着时间的推移而生成和分配在下面的[Token Distribution](#token-distribution)中所传达的时间表中。如果Livepeer中的所有角色按照协议运行，那么新生成的Token将按其绑定的股份（减费）分配给用户。转码器具有调用 `Reward()`函数的作用，以便触发新的Token分配或削减，这可以从链上可用的所有数据计算出来。

Each transcoder will be required to call `Reward()` once per round.

每个转码器都需要每轮调用一次`Reward()` 。

- Ensure that an active Transcoder is calling `Reward()`.
- Ensure that the Transcoder has not called `Reward()` yet in this round.
- Compute the number of token to mint based upon the `InflationRate`. Mint this many token.
- Calculate the Transcoder's cut based upon their `BlockRewardCut`.
- Distribute this into the Transcoder's bonded stake.
- Distribute the remainder into the delegators reward pool.
- Update the bonded amount of token to this Transcoder.
>
- 确保一个有效的转码器正在调用`Reward()`。
- 确保转码器在本轮中尚未调用`Reward()`。
- 根据`InflationRate` 计算Token到铸币的数量。铸许多Token。
- 根据他们的`BlockRewardCut`计算转码器的剪切。
- 将此分配到转码器的保税桩中。
- 将剩余部分分发给委托人奖励池。
- 将Token的保税金额更新到该转码器。

Failure to invoke `Reward()` results in the direct consequence of losing a portion of token allocations, and showing up as a ding on one’s Transcoder reputation when it comes to being elected by Delegators for the role.

未能调用`Reward()`导致丢失Token分配的一部分的直接后果，并且当由委托人为角色选择时，显示为转码器的信誉。

### Slashing 削弱

As previously mentioned, the conditions for slashing are:

如前所述，削减的条件是：

- Failing a verification
- Failing to invoke verification when required to do so
- Not performing a proportional share of the required work within the platform based upon delegated stake

- 未通过验证
- 当需要时不调用验证
- 不基于委托股份在平台内执行所需工作的比例份额

One of the benefits of building within the Ethereum ecosystem are the network effect benefits you receive from being able to build on top of other protocols such as Truebit and Swarm/SWEAR. Unfortunately, with reliance on these external systems, which themselves have external dependencies and incentives, it’s possible that a flaw or weakness in one of those protocols could result in slashing within Livepeer.

在以太坊生态系统中构建的一个好处是网络效应对你能够从其他协议（如TruteBIT和Swarm/SWEAR）上得到的好处。不幸的是，依赖于这些外部系统，它们自身具有外部依赖性和激励机制，这些协议中的一个缺陷或弱点可能导致Livepeer中的削减。

For example, if a Truebit verification job sat in their queue for a long period of time without any solver or verifier claiming it, Livepeer would fail to see the result of that verification in time before `Reward()` was called. Or if the Swarm network suffered a partition and couldn’t propagate the file to the Truebit verifier in time, then this could also create an issue.

例如，如果Truebit验证作业在他们的队列中长时间地等待，而没有任何求解者或验证者声称它，那么在调用`Reward()`之前，Livepeer将无法看到该验证的结果。或者如果Swarm网络遭受分区，并且不能及时将文件传播到TreeBIT验证器，那么这也会产生问题。

These risks can be mitigated by incentivizing these roles to be played in house by participants in the Livepeer protocol, who may find it in their best interest to serve as Truebit verifiers or Swarm nodes. But there’s also another approach which is introducing the concept of probability thresholds on the slashing parameters. Optional protocol variables such as `VerificationFailureThreshold` could be set to indicate that as long as the node passes 99% of verifications they won’t be slashed for example. This will remain a further area of research to be worked out prior to network deployment.

这些风险可以通过激励这些角色由Livepeer协议的参与者在内部发挥作用来缓解，这些参与者可能发现它们最有利于作为Truebit验证者或Swarm节点。 但另一种方法是在削减参数中引入概率阈值的概念。 可选协议变量，例如`VerificationFailureThreshold`可以设置为表示只要节点通过了99％的验证，它们就不会被削减。 这仍然是网络部署之前需要研究的另一个研究领域。

The failure to invoke verification slashing condition can be checked and invoked by any Livepeer protocol participant. There is a `FinderFee` which specifies the percent of the slash amount which the finder will receive as a reward for successfully invoking this slashing condition.

任何Livepeer协议参与者都可以检查并调用无法调用验证削减条件。 有一个`FinderFee`，它指定了取景器将作为成功调用这种削减条件的奖励而获得的斜线量的百分比。

The remainder of the slashed funds will enter the `CommonPool`, which can be burned or allocated to common uses such as further ecosystem development, according to the governance mechanism of the protocol.

剩下的大笔资金将进入`CommonPool`，根据该协议的治理机制，该CommonPool可以被烧毁或分配给常见的用途，例如进一步的生态系统开发。

### Token Distribution Token的分配

As a token that represents the ability to participate and perform work in the network through a DPoS staking algorithm, the initial Livepeer token distribution will follow the patterns of other DPoS systems which require a widely distributed genesis state.

作为代表通过DPoS算法参与和执行网络工作能力的标记，最初的Livepeer标记分布将遵循需要广泛分布的起源状态的其他DPoS系统的模式。

An initial allocation of the token will be distributed to the community at genesis and over the early stages of the network. Receipients can use it to stake into the role of Transcoder or Delegator. A portion will be allocated to groups who contributed prior work and money towards the protocol before the genesis, and a portion will be endowed for the long term development of the core project.

Token的初始分配将在网络的起源和早期阶段分发给社区。 接收者可以使用它来加入转码器或委托者的角色。 一部分将被分配给在事业发生前为协议贡献前沿工作和在创始之前向协议金额，另一部分将被赋予核心项目的长期发展。

At the launch of the network, token issuance will continue according to an inflationary schedule with token being generated at `InflationRate` per round relative to the outstanding float of token. As token is issued in proportion to stake of all bonded participants in the protocol, it serves to incentivize active participation. Participants are "protected" from this inflation, due to earning their proportional share. It is only inactive participants who are sitting on token without bonding it for participation, who will see their proportional network ownership dilluted by this inflation.

在网络启动时，Token发放将根据通货膨胀时间表继续进行，相对于未完成的Token浮动，在每轮的`InflationRate`生成Token。 由于Token与协议中所有保税参与者的利益成比例发行，因此它可以激励积极参与。 参与者受到通货膨胀的“保护”，因为他们获得了相应的份额。 只有坐在代币上而没有参与其中的非活动参与者，他们会看到他们的比例网络所有权被这种通货膨胀所冲淡。

The initial target for `InflationRate` will be set such that it aims to incentivize approximately `ParticipationRate` of the LPT to be bonded and actively participating [[19](#references)). For example, if `ParticipationRate` is 50% then incentives will exist to have half the oustanding token bonded. The inflation rate will move algorithmically each round to incent the participation target. A higher inflation rate would incent more token to be bonded, and a lower rate would lead to more people choosing liquidity rather than participation. It's this liquidity preference vs network ownership percentage tradeoff which should find equilibrium due to a number of economic factors in the network.

`InflationRate`的初始目标将被设置，目的是激励约LPT的`ParticipationRate` 被绑定和积极参与[[19](#references))。 例如，如果`ParticipationRate`为50％，那么奖励将存在一半的优秀代币保税。 通货膨胀率将通过算法循环来提升参与目标。 更高的通货膨胀率会更有利于保税，而更低的利率会导致更多的人选择流动性而不是参与。 正是这种流动性偏好与网络所有权百分比之间的折衷，由于网络中的许多经济因素，应该找到均衡。

### Governance 治理

The role of governance within the Livepeer protocol is intended to be three fold:

在Livepeer协议中治理的作用有三个目的：

1. Determine the burning or appropriation of common funds which were slashed from misbehaving nodes.
2. Adjust network parameters to ensure a healthy, thriving network which is valuable to broadcasters.
3. Invoke proposed protocol updates in a decentralized fashion.
>
1. 确定由不当行为节点削减的共同基金的燃烧或挪用。
2. 调整网络参数，以确保健康、繁荣的网络，这对直播发布者是有价值的。
3. 以去中心化的方式调用提议的协议更新。

Many of the network parameters referenced in this document such as `UnbondingPeriod`, `RoundLength`, `ParticipationRate`, and `VerificationRate` are adjustable. Proposals for adjustments to these parameters can be submitted, and the governance process, including voting by transcoders in proportion to their delegated stake, will determine adoption of these changes automatically within the protocol. The detailed spec for governance is left for another document. [See more here](https://github.com/livepeer/wiki/wiki/Governance). 

本文档中引用的许多网络参数（如`UnbondingPeriod`, `RoundLength`, `ParticipationRate`, 和 `VerificationRate`）均可调整。 可以提交对这些参数进行调整的建议，并且治理过程（包括转码器根据其委派的股份投票）将决定在协议中自动采用这些更改。 治理的详细规范留给其他文档。  [See more here](https://github.com/livepeer/wiki/wiki/Governance)。

## Attacks 攻击

This section contains a survey of the various ways that malicious actors may try to attack the Livepeer network. We use a rational attacker model in which the attacker makes decisions based upon their own economic self interest. A number of attacks are mitigated via it being unprofitable to conduct such attacks, but we also strive to ensure that at the worst the network suffers decrease of efficiency in the case of a sustained unprofitable attack, and doesn't suffer a failure.

本节包含了恶意参与者可能试图攻击Livepeer网络的各种方式的调查。我们使用一个理性攻击者模型，其中攻击者根据自己的经济自利作出决定。许多攻击通过无利可图地进行这种攻击而减轻，但我们也努力确保在最坏的情况下，网络遭受持续的无利可图攻击的效率降低，并且不会遭受失败。

### Consensus Attacks 共识攻击

As mentioned previously, consensus in the Livepeer ecosystem is provided by the underlying blockchain platform (Ethereum for example). 51% attacks, double spends of Livepeer Token, and forks of the network would require the same resources and cost-of-attack as Ethereum itself.

如前所述，Livepeer生态系统中的一致性是由底层区块链平台（例如以太坊）提供的。51%个攻击，双LivepeerToken和网络的叉的两个花费将需要相同的资源和攻击成本，如以太坊本身。

Livepeer is a staked based protocol, and while Transcoders have the role of participating in the work verification process and the token reward distribution process, they actually do not have the role of validating or accepting other Transcoders' work. There is no concept of a chain, nor is there validation of previous blocks. There simply exists the economic incentives to verify one's own work and distribute one's own portion of token allocations when it is one's turn. As such, attacks that are seen in a proof of stake protocols such as the Long Range Attack, the Nothing at Stake problem, and The Bribe Attack don't apply, as there is no opportunity to attempt to sign multiple blocks or attempt to create a longer chain from an earlier state. However, one should be aware that as the underlying blockchain migrates to proof of stake, these attacks do threaten to undermine Livepeer if the benefit of carrying them out on Livepeer were to exceed the cost of attack on Ethereum itself.

Livepeer是一个基于协议的协议，虽然转码器具有参与工作验证过程和Token奖励分配过程的作用，但实际上它们不具有验证或接受其他转码器工作的角色。 没有一个链的概念，也没有验证以前的块。 只是存在经济激励来验证自己的工作，并在轮到时分配自己的Token分配部分。 因此，在远程攻击证明，风险无关和贿赂攻击等利益协议证明中看到的攻击不适用，因为没有机会尝试签署多个块或尝试创建 来自较早状态的较长链。 然而，人们应该意识到，随着潜在区块链迁移到股份证明，如果在Livepeer上实施这些攻击的好处超过了以太坊本身的攻击成本，这些攻击确实会威胁到Livepeer。

While relying on the security of the underlying blockchain is nice for prevention of consensus attacks, there still exists a class of quality and efficiency attacks that can harm the Livepeer network.

虽然依靠底层区块链的安全性对于防止共识攻击是很好的，但仍然存在一类质量和效率攻击可能会损害Livepeer网络。

### DDoS 

Denial of Service in Livepeer can go two ways:

Livepeer中的拒绝服务有两种方式：

1. A Transcoder can try to prevent or slow down a Broadcaster from getting their encoded stream out to the network by accepting a job but refusing to transcode.
2. A Broadcaster can prevent a Transcoder from being able to do the job that they believe they were assigned by refusing to send them segments.
>
1.转码器可以尝试阻止或减慢直播发布者通过接受作业但拒绝转码将其编码流发送到网络。
2.直播发布者可以阻止转码器完成他们认为是通过拒绝发送段来分配的工作。

Both attacks have a cost and can be mitigated, with slight annoyance.

这两种攻击都有代价，而且可以减轻，有轻微的烦恼。

In the first case, a Transcoder has to pay to claim their availability on chain. If they are not going to receive a fee because they didn't do the work, then they're throwing ETH away. The Broadcaster can just resubmit the job and be assigned a new Transcoder. One potential option for scalability is that the protocol can identify a number of valid Transcoders in priority order instead of just one, and this way the Broadcaster can just move on without another on chain transaction. Additionally, all stats about accepted jobs and average # of segments transcoded/job, etc, can be calculated from on-chain data, and delegators would use this as input into their decision about whom to delegate towards. Behave poorly and lose your role.

在第一种情况下，转码喆必须支付声明他们可用性在链上。如果他们不收取费用，因为他们没有做这项工作，那么他们就扔了。直播发布者可以重新提交任务并分配一个新的转码器。可扩展性的一个潜在选择是，协议可以以优先级顺序标识多个有效的转码器，而不是仅一个，并且这样，直播发布者可以在没有另一个链上事务的情况下继续前进。此外，关于接受的工作的所有统计数据和平均代码段的转码/作业等，都可以从链数据中计算出来，并且委托人将其作为输入来决定他们向谁委托。表现不好，失去你的角色。

In the case of a Broadcaster preventing a Transcoder from doing work, this is merely a capacity planning calculation. A Transcoding node can maintain records of its capacity for concurrent jobs, likelihood of a job being active/inactive, and ensure that it always believes it will have capacity for the work that it claims. Simply ignoring or calling `EndJob()` on a node that's refusing to send segments hardly hurts the Transcoder.

如果直播发布者阻止转码器工作，这只是一个容量规划计算。 代码转换节点可以维护并发作业的容量记录，作业处于活动/不活动状态的可能性，并确保它始终认为它可以处理它声称的作业。 简单地忽略或调用拒绝发送段的节点上的`EndJob()`几乎不会伤害转码器。

### Useless or Self Dealing Transcoder 无用的或者自消耗的转码器

If a Transcoder has enough stake to maintain their position, they could theoretically list a 100% `BlockRewardCut`, 0% `FeeShare`, and charge a high `PricePerSegment` such that they would never have to do any work, yet could collect their token allocation. This is prevented by the `CompetitivenessTolerance` which requires them to contribute some amount of valid work. Additionally, because of the transaction costs of participating in the protocol incurred by Transcoders, it would be more profitable for them to simply stake their token toward a valid Transcoder who was sharing fees with them, than it would be to act as a useless Transcoder who would receive no fees to speak of.

如果转码器拥有足够的股份来维持其位置，他们理论上可以列出100％ `BlockRewardCut`，0％`FeeShare`，并收取高价`PricePerSegment`，以便他们永远不必做任何工作，但可以收集他们的 Token分配。 这是由`CompetitivenessTolerance` （竞争力容忍度）阻止的，要求他们贡献一定数量的有效工作。 另外，由于参与转码器产生的协议的交易成本，他们只需将他们的代币放在与他们分享费用的有效转码器上就会更有利，而不是像一个无用的转码器那样工作 将不收取任何费用。

A misbehaving Transcoder who is outputting invalid output would quickly get slashed down to the point of their stake being reduced too low to actually keep their job and receive any work.

输出无效输出的行为不端的转码器很快就会被削减到他们的股份被降低到不能实际保留他们的工作和接收任何工作的程度。

### Transcoder Griefing   转码器 Griefing

If a Broadcaster wanted to make the protocol very expensive to operate for a transcoder, it could send transcoders non-consecutive segment numbers. This is because transcoders can claim work for a continuous range of segment numbers in a single transaction, but would have to make many transactions to claim work across random segment number ranges. This can be defended against by the following options:

如果一个直播发布者想要使协议对于转码器非常昂贵，它可以发送转码器非连续的段号。这是因为在单个事务中，转码器可以要求对连续数量的段编号进行工作，但必须进行许多事务以在随机段编号范围内请求工作。这可以通过以下选项进行辩护：

1. Transcoder calls `EndJob()` and doesn't bother doing the work or attempting to collect the fees. 
2. Protocol implements on chain parsing or better segment claim encoding in order to reduce fees associated with claiming non-consecutive segments in a single call.
3. Simply ignore the segments and never claim the work.
>
1. 转码器调用`EndJob()`，不费心做这项工作，也不想收取费用。
2. 协议实现链分析或更好的分段请求编码，以减少与在单个呼叫中请求非连续段相关联的费用。
3. 简单地忽略分段，从不要求工作。

This attack has a high cost to a broadcaster since they must have a deposit and submit jobs on chain in order to even get assigned to a transcoder in the first place. They have the ability to make life annoying for a transcoder and potentially lose efficiency, but not cause damage to the network.

这种攻击对直播发布者来说成本很高，因为他们必须有一个存款，并提交工作链上，甚至分配到一个转码器在第一位。他们有能力使生活烦扰的转码器，并可能失去效率，但不造成损害网络。

### Chain Reorg 链重组

When a broadcaster submits a job to the Livepeer Smart Contract, the protocol uses the current block hash to determine which transcoder will be assigned the job. Reorganizations of the underlying blockchain can cause confusion in this scenario. While this is not "an attack" directly, a transcoder will be valid one second, and then upon reorganization, will no longer be valid. When a reorg is detected the broadcaster can either redirect the stream towards the new valid transcoder, or the protocol can detect uncle blocks that are included in the main chain, and consider a transcoder to be valid if an uncle block within a given threshold would have made them valid. 

当直播发布者向Livepeer智能合约提交作业时，协议使用当前区块哈希来确定哪个转码器将被分配作业。 底层区块链的重组可能会导致混淆。 尽管这不是直接的“攻击”，但转码器将有效一秒，然后在重组时，将不再有效。 当检测到重组时，直播发布者可以将流重定向到新的有效转码器，或者协议可以检测包含在主链中的叔代码块，并且如果在给定阈值内的叔代码块将具有转码器是有效的使其有效。

## Live Video Distribution 直播视频分发 ###########################################

This whitepaper has largely focused on the economic incentives and protocol for ensuring proper transcoding of live video, which is necessary to support adaptive bitrate streaming and reach every device. But equally important is the distribution of video throughout the network so that it can be consumed with high quality and low latency. The economics of distribution rely on tit-for-tat bandwidth accounting as popularized by Bittorrent, and extended via protocols like SWAP [[13](#references)]. As a simplification, nodes pay to request a segment of video, and nodes get paid to serve a segment of video. If a node already has a segment and can serve it to multiple requestors, it is profitable. We call this type of node, a Relay node.

本白皮书主要集中在确保实时视频正确转码的经济激励和协议，这对于支持自适应比特率流和每个设备都是必要的。 但同样重要的是在整个网络中分配视频，以便能够以高质量和低延迟消费视频。 分发的经济性依赖于BitTorrent流行的的tit-for-tat带宽核算，并通过诸如 SWAP [[13](#references)] 等协议进行扩展。 作为简化，节点付费请求一段视频，节点获得付费以服务一段视频。 如果一个节点已经有一个段并且可以将它提供给多个请求者，这是有利可图的。 我们称这种类型的节点为中继节点。

Different incentives exist when it comes to bandwidth for nodes playing different roles in the network.

当涉及网络中扮演不同角色的节点的带宽时，存在不同的激励机制。

* Consumers may be willing to exchange upstream bandwidth to serve the content to additional Consumers in exchange for being able to consume the video themselves free of charge. See systems like Webtorrent [[14](#references)].
* Broadcasters serve as origin nodes and may want to charge for consumption of the video, or may want to subsidize the cost of bandwidth so that everyone can access their video for free.
* Transcoders and Relay nodes are willing to provide bandwidth in service of distributing video as long as it is profitable. This is similar to the role of traditional CDNs.
>
* 消费者可能愿意交换上行带宽以将内容提供给额外的消费者，以换取自己能够免费使用视频。 请参阅 Webtorrent [[14](#references)] 之类的系统。
* 直播发布者作为原始节点，可能希望为视频消费收费，或者可能希望补贴带宽成本，以便每个人都可以免费访问他们的视频。
* 只要有利可图，转码器和中继节点都愿意提供分配视频服务的带宽。 这与传统CDN的作用相似。

With `Segments` as the core unit of data flowing through the network, it is possible to do tit-for-tat bandwidth accounting using ETH as the basis for settlement. We borrow the Chequebook Contract abstraction from Swarm [[6](#references)] as a method of offchain payment passing with on chain settlement. Future developments in the ecosystem including the Raiden Network [[15](#references)] may allow of payment channels to be used for this purpose as well. Since token transfer is native to the protocol, it is also possible to embed pricing associated with content directly into the protocol. A broadcaster can charge for their time or content directly, and nodes will opt into this transfer of value by paying a higher price/segment which will flow back to the broadcaster.

利用`Segments`作为数据流经网络的核心单元，可以使用ETH作为结算基础来进行tit-for-tat的带宽记帐。 我们借用的支票簿合同抽象的Swarm[[6](#references)]作为链式结算通过的离线支付方式。 包括雷电网络[[15](#references)] 在内的生态系统未来的发展也可能允许支付渠道用于此目的。 由于Token传输是协议的本地特性，因此也可以将与内容相关的定价直接嵌入到协议中。 直播发布者可以直接对其时间或内容收费，并且节点将通过支付更高的价格/分段来选择进行该价值转移，该价格/分段将流回直播发布者。

What's important to note is that while bandwidth accounting can be used to make it profitable to run Relay Nodes which just pass video segments around the network to add capacity, a-la a CDN, these nodes are purely incentivized by demand for the content, and not incentivized by new token allocations. In fact, the output of Livepeer can be inserted into a traditional CDN (like Amazon S3, Cloudflare, etc) or decentralized CDN (like IPFS or Swarm). Development of this peer-to-peer protocol for video segment distribution itself will be an ongoing opportunity for optimization and improvement in performance.

需要注意的是，虽然带宽记帐可以用于运行仅通过网络传输视频片段以增加容量的中继节点（CDN），但这些节点完全被对内容的需求所激励，并且 没有被新的Token分配激励。 事实上，Livepeer的输出可以插入传统CDN（如Amazon S3，Cloudflare等）或去中心化式CDN（如IPFS或Swarm）中。 这种用于视频段分配的P2P协议的开发本身将是优化和改善性能的持续机会。

Peer-to-peer CDNs have been shown to reduce 80-98% of bandwidth requirements on an origin CDN server [[17](#references)], and the token mechanics seen in decentralized networks can align stakeholders for the development and maintenance of an open version of the proprietary P2P CDNs that exist today. The PPSPP Protocol [[18](#references)] serves as a viable candidate for an open implementation that focuses on delivery of live content.

P2P的CDN已经被证明可以减少原始CDN服务器上的80-98％的带宽需求[[17](#references)]，去中心化网络中看到的token机制可以让利益相关者协调开发和维护今天存在的专有P2P得CDN的开放版本。 PPSPP协议[[18](#references)]作为开放实施的可行候选，专注于提供实时内容。

As non-critical to the cryptoeconomics of the Livepeer protocol, the details are spared from this document, but the interested can [follow along here](https://github.com/livepeer/go-livepeer) with the development, and look for a future document addressing purely the video distribution protocol.

对于Livepeer协议的密码组学s而言，并非至关重要，详细信息不在此文件中，但感兴趣的人可以[在此处](https://github.com/livepeer/go-livepeer) 进行开发，并查看为将来的文件寻址纯粹的视频分配协议。

## Use Cases 使用案例 ###########################################

The Livepeer project is concerned with decentralizing one-to-many live video broadcast (multicast). This is the truest form of media distribution, as it allows a broadcaster to connect directly with their audience in a first-hand manner, free from alterations, after-the-fact interpretation, and spin. It gives everyone a platform to have a voice. Existing centralized solutions can suffer from censorship, third party control over user data/relationship/monetization, and inefficient cost structures around payment for the service. Here are some of the logical use cases for applications and services to be built on top of Livepeer.

Livepeer项目涉及去中心化一到多个视频直播（多播）。这是最真实的媒体分发形式，因为它允许直播发布者以直接的方式直接与听众联系，不受事实的解释和旋转的影响。它给每个人提供了一个发言的平台。现有的集中式解决方案可能遭受审查，第三方对用户数据/关系/货币化的控制，以及围绕服务付费的低效成本结构。下面是应用程序和服务在Livepeer之上构建的一些逻辑用例。

### Pay-As-You-Go Content Consumption 对内容消费即付即用

With a transfer of value transaction baked into the protocol, it is now possible for broadcasters to charge viewers directly for the consumption of their live broadcast, without requiring a credit card, account, or control over user identity via a centralized platform. This has applications in education (pay to attend an online course), events (pay to view a concert or live sporting event), entertainment (pay to watch a gamer or performer's live stream), and many other use cases - all while preserving the privacy of the viewer, and allowing them to pay for only what they consume directly to the broadcaster.

通过将价值交易转移到协议中，现在直播发布者可以直接向观众收取其直播的费用，而不需要通过集中式平台对信用卡、账户或用户身份进行控制。这在教育（付费参加在线课程）、活动（支付观看音乐会或直播体育赛事）、娱乐（支付观看游戏者或表演者的直播流）以及许多其他用例中都有应用-都在保持观众的隐私，并允许他们只直接支付给直播发布者。

### Auto-scaling Social Video Services 自动缩放社交视频服务

One of the challenges of building consumer video services today is scaling infrastructure to support the demand for the growing number of streams and growing number of consumers as new users are added. A service layer that easily lets developers begin building their video solution on top of the Livepeer Network, which will automatically scale to support any number of streams and viewers as they go, will be a welcome solution to infrastructure developers who would otherwise have to continue provisioning servers, licensing media servers, and efficiently manage resources to handle spikes.

如今构建消费视频服务面临的挑战之一是扩展基础设施以支持不断增加的流的需求和随着新用户的加入而不断增长的消费者数量。一个易于让开发者开始在Livepeer网络之上构建他们的视频解决方案的服务层，它将自动缩放以支持任何数量的流和观众，这将是一个值得欢迎的解决方案。IONE服务器，授权媒体服务器，并有效地管理资源来处理尖峰。

### Uncensorable Live Journalism 不能现场直播的新闻业

Current platforms such as Twitter and Facebook provide amazing live video solutions for reaching a large audience, but they're also the first to get blocked or censored in a variety of political conflict situations. Use of a decentralized network such as Livepeer would render it nearly impossible to prevent the word from getting out as to what is really going on on the ground in realtime.

目前的平台，如Twitter和Facebook提供惊人的现场视频解决方案，以达到广大观众，但他们也是第一个被封锁或审查在各种政治冲突的情况下。使用一个去中心化的网络，例如Livepeer会使它几乎不可能防止这个词真正出现在地面上。

### Video Enabled DApps 启动视频的DApps

Decentralized apps (DApps) are beginning to emerge, driven largely by the Ethereum ecosystem. However, to date there hasn't been a viable solution for embedding live video within a DApp without using a centralized solution or limiting the number of consuming clients based on the constraints of WebRTC. By introducing Livepeer to the stack, an application can be fully decentralized, yet still contain live video, at scale, to as many users as wish to consume it.

去中心化应用（Dapp）开始出现，主要是由生态系统驱动的。然而，到目前为止，还没有一种可行的解决方案，在不使用集中式解决方案的情况下嵌入DAPP中的直播视频，或者基于WebRTC的约束来限制消费客户端的数量。通过将Livepeer引入到堆栈中，应用程序可以完全去中心化，但仍然包含实时视频，在规模上，与希望消费的用户一样多。

## Summary 总结 ###########################################

In summary, the Livepeer protocol incentivizes nodes to contribute their processing and bandwidth to the network in service of transcoding and distributing live video. The verification of work is solved by a scalable extension on top of the Truebit protocol which incentivizes nodes to perform transcoding operations correctly in order to earn their fees and token allocations and preserve their value earning role as a transcoder. The gamification of the network and false work problem is solved via the economics of the delegated proof of stake block reward accounting. It becomes more economically rational to simply stake one's tokens towards a value adding node than to pay fees into the network to be distributed to other delegators when performing work that there wasn't actually real demand for.

总之，Livepeer协议激励节点在转码和分发实时视频的服务中贡献他们的处理和带宽到网络。工作的验证通过TtrueBIT协议上的可扩展扩展来解决，Truebit协议激励节点正确地执行代码转换操作，以赚取它们的费用和Token分配，并保持其作为转码器的价值获取角色。通过股权分置奖励会计委派证明的经济学解决了网络的虚假化和虚假工作问题。在执行实际上没有实际需求的工作时，简单地将一个Token绑定到一个增值节点比在网络上支付费用来分配给其他委托人更加经济合理。

The end result is a scalable, pay-as-you-go network for decentralized live video broadcast - a missing layer in the web3 stack that Livepeer seeks to fill.

最终的结果是一个可扩展的即付即用网络，用于去中心化式视频直播 - Livepeer寻求填补的web3堆栈中缺失的一层。

## Appendix 附录 ###########################################

### Livepeer Protocol Parameter Reference  Livepeer 协议参数参考

| Parameter Name | Description | Example Value |
|----|------|---|
| `T` | Segment length in seconds | 2 seconds |
| `N` | Number of active transcoders | 144 |
| `RoundLength` | Length of time between election of a new round of transcoders | 1 day |
| `InflationRate` | The current target inflation rate per round of LPT. (Moves algorithmically). | .04% (equivalent to 15%/year) |
| `ParticipationRate` | The target percent of token bonded vs liquid. | 50% |
| `RoundLockAmount` | Transcoders rates lock in for this percentage of a round at the end of a round so that delegators can review and delegate accordingly without worrying about last minute rate changes. | 10% == 2.4 hours |
| `UnbondingPeriod` | Time between entering unbonding state, and ability to withdraw the funds. | 1 month |
| `VerificationPeriod` | The deadline for verifying a job claim after submission of the job claim. This also serves as the minimum period that a receipt of data persistence must be provided in the decentralized storage solution. | 6 hours |
| `VerificationRate` | The % of segments that will be verified. | 1/500 |
| `FailedVerificationSlashAmount` | % to slash in the case of a failed verification (beyond the potential allowed failure threshold) | 5% |
| `MissedRewardSlashAmount` | % to slash in the case of missing a block reward round (Maybe only do this in the case of n consecutive misses) | 3% |
| `MissedVerificationSlashAmount` | % to slash in the case the transcoder didn’t call verification | 10% |
| `CompetitivenessTolerance` | If all transcoders were always available and set the same price and fees, they would receive work in proportion to their stake. This parameter sets a % that they have to be within this target work % to be eligible for token allocation. This prevents transcoders from doing very little share of work relative to their stake. | 90% (extreme example. With 100 transcoders and 100,000 segments, this means I am ok if I only did 100 segments (10% of the 1000 I was supposed to do)). |
| `*SlashingThresholds` (TBD) | Placeholder to indicate that we may not slash on all failures, only if they exceed some threshold % of failure rate. | |
| `VerificationFailureThreshold` | % of verifications you can fail without being slashed. Useful because of external dependencies like Swarm/Truebit that could cause sporadic failure. | 1% |
| `FinderFee` | % of slash amount that the finder will receive as compensation. | 5% |
| `SlashingPeriod` | The deadline for invoking a slashing condition after the `VerificationPeriod` has completed. | 1 hour |


| 参数名称 | 描述 | 例如值 |
|----|------|---|
| `T` | 秒段长度 | 2 seconds |
| `N` | 能用的转码器数量 | 144 |
| `RoundLength` | 选择新一轮转码器之间的时长 | 1 day |
| `InflationRate` | 目前每轮LPT的目标通胀率（算法移动） | .04% (相当于 15%/year) |
| `ParticipationRate` | token与流通盘的目标百分比 | 50% |
| `RoundLockAmount` | 转码器的费率在轮次结束时锁定该比例的这个百分比，以便委派者可以相应地进行审查和委托，而不用担心最后一刻的费率变化 | 10% == 2.4 hours |
| `UnbondingPeriod` | 进入无约束状态和撤回资金的能力之间的时间 | 1 month |
| `VerificationPeriod` | 提交工作索赔后验证工作声明的最后期限。 这也是在去中心化存储解决方案中必须提供数据持久性接收的最短时间 | 6 hours |
| `VerificationRate` | 将被验证的分段的百分比 | 1/500 |
| `FailedVerificationSlashAmount` | 在失败验证（超出潜在的允许失败阈值）的情况下可以削减的百分比 | 5% |
| `MissedRewardSlashAmount` | 在缺少一个区块奖励轮的情况下可以削减的百分比（也许只有在连续n次失败的情况下才会这样做） | 3% |
| `MissedVerificationSlashAmount` | 在换码转器未调用验证的情况可以削减的百分比 | 10% |
| `CompetitivenessTolerance` | 如果所有转码器总是可用并且设置相同的价格和费用，则他们将按照他们的股份获得相应的工作。 该参数设置了必须在此目标工作百分比内才能符合token分配的百分比。 这可以防止转码器相对于他们的股份做少量工作 | 90% （极端的例子，100个转码器和100,000个代码段，这意味着如果我只做了100个代码段（1000个代码段中的10％应该这样做)）|
| `*SlashingThresholds` (TBD) | Placeholder to indicate that we may not slash on all failures, only if they exceed some threshold % of failure rate. | 占位符表明我们可能不会对所有失败进行削减，只要它们超过失败率的阈值百分比 |
| `VerificationFailureThreshold` | 可以在不被削减的情况下失败的验证百分比。因为像Swarm/Truebit这样可能导致零星故障的外部依赖关系很有用 | 1% |
| `FinderFee` | % of slash amount that the finder will receive as compensation. 发现者作为补偿的金额削减的百分比 | 5% |
| `SlashingPeriod` | 在 `VerificationPeriod` 结束后，调用截取条件的最后期限 | 1 hour |

### Livepeer Protocol Transaction Types Livepeer协议事务类型

| Transaction | Description |
|----|------|
| `Bond()` | Bond stake towards a transcoder. |
| `Unbond()` | Enter the unbonding state for the fixed `UnbondingPeriod`. |
| `Transcoder()` | Declare your intentions as a transcoder. |
| `ResignAsTranscoder()` | Resign your intentions as a transcoder. |
| `TranscodeAvailability()` | This transcoder is currently open to accepting another job. They’re in the pool to be assigned randomly on new job submissions. |
| `Job()` | Submit a transcoding job on chain. |
| `EndJob()` | End the job to relinquish transcoding responsibility. |
| `Deposit()` | Submit a deposit on chain that will be used and drawn against to pay for jobs. |
| `Withdraw()` | Withdraw from deposit and unbonded stake. |
| `ClaimWork()` | End the transcode job and make the claim of which segments you can prove you’ve transcoded via segment range and merkle root. |
| `DistributeFees()` | Transcoder claims the fees for a particular claim after verification. |
| `Reward()` | Does all the verifications on chain to either slash or distribute token allocations. Can only be invoked by a transcoder who is active in the current round, once per round. |
| `Verify()` | Transcoder provides the transcode claims for segments which will be verified along with merkle proofs for comparison with merkle root from `ClaimWork()`. Explicitly call Truebit to perform verification. |
| `InitializeRound()` | This transaction needs to be invoked once after the new round's start block to initialize the new active transcoder pool. |
| `UpdateDelegatorStake()` | This allows a delegator to claim their fees + token allocation from previous rounds. It's invoked automatically through unbonding and bonding, but it serves as a failsafe in case the delegator would like to update without changing state. |
| `*GovernanceTransactions()` | TBD  |

| 事务 | 描述 |
|----|------|
| `Bond()` | 债券转码 |
| `Unbond()` | 输入固定的 `UnbondingPeriod` 的解除绑定状态 |
| `Transcoder()` | 宣布你的意图是一个转码器 |
| `ResignAsTranscoder()` | 作为转码器辞去你的意图 |
| `TranscodeAvailability()` | 该转码器目前可以接受另一份工作。 他们在池中随机分配新的工作提交 |
| `Job()` | 提交链上的转码作业 |
| `EndJob()` | 结束工作以放弃转码责任 |
| `Deposit()` | 提交一份将被用来支付工作的链条上的定金 |
| `Withdraw()` | 撤回存款和无担保股份 |
| `ClaimWork()` | 结束转码工作，并声明哪些段可以证明您已经通过段范围和merkle根进行了转码 |
| `DistributeFees()` | 转码器在验证后声明特定索赔的费用。 |
| `Reward()` | 链上的所有验证是否会削减或分配Token分配。 只能由当前一轮活动的转码器调用，每轮一次 |
| `Verify()` | 转码器提供了分段的转码声明，这些代码段将与来自`ClaimWork()`的merkle根的merkle证明一起验证。 显式调用Truebit来执行验证 |
| `InitializeRound()` | 该事务需要在新一轮启动块之后调用一次以初始化新的活动转码器 |
| `UpdateDelegatorStake()` | 这允许授权人从前几轮申请费用+Token分配。 它是通过非绑定和绑定自动调用的，但是如果委托者希望在不更改状态的情况下进行更新，它可以作为故障安全。 |
| `*GovernanceTransactions()` | TBD  |

## References 参考 ###########################################

1. Ethereum White Paper - Vitalik Buterin - Ethereum Wiki - <https://github.com/ethereum/wiki/wiki/White-Paper>
2. Fat Protocols - Joel Monegro - USV Blog - <http://www.usv.com/blog/fat-protocols>
3. Crypto Tokens and the Coming Age of Protocol Innovation - Albert Wenger - <http://continuations.com/post/148098927445/crypto-tokens-and-the-coming-age-of-protocol>
4. The Case For SectorCoins - Eric Tang - <https://medium.com/@ericxtang/case-for-sectorcoins-b70a7c820c2d#.7892n4a57>
5. Delegated Proof-of-Stake Consensus - Daniel Larimer - <https://bitshares.org/technology/delegated-proof-of-stake-consensus/>
6. Truebit - Jason Teutsch and Christian Reitweisner - <https://people.cs.uchicago.edu/~teutsch/papers/truebit.pdf>
7. swap, swear and swindle, incentive system for swarm - viktor trón, aron fischer, dániel a. nagy, zsolt felföldi, nick johnson - <http://swarm-gateways.net/bzz:/theswarm.eth/ethersphere/orange-papers/1/sw%5E3.pdf>
8. Kademlia: A Peer-to-peer Information System Based On The XOR Metric - Petar Maymounkov and David Mazieres <https://pdos.csail.mit.edu/~petar/papers/maymounkov-kademlia-lncs.pdf>
9. Steem Whitepaper - Daniel Larimer, Ned Scott, Valentine Zavgorodnev, Benjamin Johnson, James Calfee, Michael Vandeberg - <https://steem.io/SteemWhitePaper.pdf>
10. Introducing Casper "the Friendly Ghost" - Vlad Zamfir - <https://blog.ethereum.org/2015/08/01/introducing-casper-friendly-ghost/>
11. Tendermint Docs - Jae Kwon and Ethan Buchman - <https://tendermint.com/docs>
12. Swarm - <http://swarm-gateways.net/bzz:/theswarm.eth/>
13. Incentives Build Robustness in BitTorrent - Bram Cohen - <http://bittorrent.org/bittorrentecon.pdf>
14. WebTorrent - <https://webtorrent.io/>
15. Raiden Network - <http://raiden.network/>
16. ERC20 Token Standard - <https://github.com/ethereum/EIPs/issues/20>
17. Peer5 leverages viewers’ devices for a P2P approach to streaming video - <https://techcrunch.com/2017/01/26/peer5-y-combinator/>
18. Peer-to-Peer Streaming Peer Protocol - <https://tools.ietf.org/html/rfc7574>
19. Inflation and Participation in Stake Based Protocols - Doug Petkanics - <https://medium.com/@petkanics/inflation-and-participation-in-stake-based-token-protocols-1593688612bf>
