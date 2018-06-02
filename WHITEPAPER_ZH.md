# Livepeer Whitepaper

**分布式视频流媒体传输协议 及 经济激励**

**Protocol and Economic Incentives For a Decentralized Live Video Streaming Network**

Doug Petkanics <doug@livepeer.org>  
Eric Tang <eric@livepeer.org>

## 摘要 ###########################################

The Livepeer project aims to deliver a live video streaming network protocol that is fully decentralized, highly scalable, crypto token incentivized, and results in a solution which can serve as the live media layer in the decentralized development (web3) stack. In addition, Livepeer is meant to provide an economically efficient alternative to centralized broadcasting solutions for any existing broadcaster. In this document we describe the Livepeer Protocol - a delegated stake based protocol for incentivizing participants in a live video broadcast network in a game-theoretically secure way. We present solutions for the scalable verification of decentralized work, as well as the prevention of useless work in an attempt to game the token allocations in an inflationary system.

Livepeer项目旨在提供实时视频流媒体网络协议，该协议完全分散，高度可扩展，加密令牌被激励，并产生可用作分散式开发（web3）堆栈中的实时媒体层的解决方案。 此外，Livepeer旨在为任何现有广播公司的集中广播解决方案提供经济高效的替代方案。 在本文中，我们描述了Livepeer协议 - 一种基于赌注的委托协议，用于以理论上安全的方式激励现场视频广播网络中的参与者。 我们提出了分散工作的可扩展验证解决方案，以及防止无用工作，试图在通货膨胀系统中进行令牌分配。

Livepeer项目旨在提供一种完全分散、高度可扩展、加密令牌激励的实时视频流网络协议，并产生一种解决方案，该解决方案可以作为分散式开发（Web3）堆栈中的实况媒体层。此外，LIVEPER旨在为任何现有的广播提供一种经济高效的集中广播解决方案。在本文中，我们描述了LIVEPELL协议——基于博弈的理论上安全的方法，用于授权直播视频广播网络中的参与者。我们提出的解决方案，分散工作的可扩展性验证，以及防止无用的工作，试图在通货膨胀系统中的令牌分配游戏。

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
        - [Broadcast + Transcoding Job 广播+转码作业](#broadcast--transcoding-job-广播转码作业)
            - [Preprocessing 预处理](#preprocessing-预处理)
            - [The Job 作业](#the-job-作业)
            - [End Job](#end-job)
        - [Verification of Work](#verification-of-work)
            - [A Note On Truebit](#a-note-on-truebit)
        - [Token Generation  Token的生产](#token-generation--token的生产)
        - [Slashing](#slashing)
        - [Token Distribution](#token-distribution)
        - [Governance](#governance)
    - [Attacks](#attacks)
        - [Consensus Attacks](#consensus-attacks)
        - [DDoS](#ddos)
        - [Useless or Self Dealing Transcoder](#useless-or-self-dealing-transcoder)
        - [Transcoder Griefing](#transcoder-griefing)
        - [Chain Reorg](#chain-reorg)
    - [Live Video Distribution](#live-video-distribution)
    - [Use Cases](#use-cases)
        - [Pay-As-You-Go Content Consumption](#pay-as-you-go-content-consumption)
        - [Auto-scaling Social Video Services](#auto-scaling-social-video-services)
        - [Uncensorable Live Journalism](#uncensorable-live-journalism)
        - [Video Enabled DApps](#video-enabled-dapps)
    - [Summary](#summary)
    - [Appendix](#appendix)
        - [Livepeer Protocol Parameter Reference](#livepeer-protocol-parameter-reference)
        - [Livepeer Protocol Transaction Types](#livepeer-protocol-transaction-types)
    - [References](#references)

<!-- /TOC -->

*Note: This document will continue to be updated along the path to the 1.0 deployment of Livepeer. Check back for changes.*

*注意：本文档将继续沿着1.0部署Livepeer的路径进行更新。 检查更改。*

## 介绍和背景 ###########################################

The vision of the decentralized web has begun to be realized over the past couple years with the emergence of networks like [Ethereum](http://ethereum.org) to enable trustless computing, [Swarm](http://swarm-gateways.net/bzz:/theswarm.eth/) and [IPFS/Filecoin](http://ipfs.io) to enable decentralized storage and content distribution, Bitcoin and various token projects to facilitate p2p transfer of value, and decentralized name registries like [Blockstack](http://blockstack.org) and [ENS](http://ens.readthedocs.io/en/latest/introduction.html) to provide human accessible names to content and identities. These elements form the basis for decentralized applications (DApps) to be built in the form of largely static or infrequently updated web or mobile content, but at the moment DApps still lack the ability to include streaming media and data in an open and decentralized way. The goal of the Livepeer project is to decentralize live video broadcast over the internet.

在过去的几年中，去中心化网络的愿景已经开始实现，随着[Ethereum](http://ethereum.org)等网络的出现，使得计算是可信任的，[Swarm](http://swarm-gateways.net/bzz:/theswarm.eth/) 和  [IPFS/Filecoin](http://ipfs.io)，以实现分散的存储和内容分发，比特币和各种代币项目以促进p2p价值转移，以及分散的名称注册管理机构 如[Blockstack](http://blockstack.org)和[ENS](http://ens.readthedocs.io/en/latest/introduction.html) 为内容和身份提供人性化的名称。 这些元素构成了分布式应用程序(DApps)的基础，以大部分静态或不常更新的Web或移动内容的形式构建，但目前DApps仍然缺乏以开放和分散的方式包含流媒体和数据的能力。 Livepeer项目的目标是通过互联网分散实况视频广播。

The [Livepeer Project Overview](https://github.com/livepeer/wiki/wiki/Project-Overview) provides a nice introduction to the current state of live video on the internet. This whitepaper will largely focus on the cryptoeconomic protocol details of Livepeer, rather than the business case, but in summary the overview describes the current state of live streaming as growing at a rapid pace, centralized, and expensive. On the other hand, a fully decentralized P2P solution, where nodes contributed their own computation and bandwidth in service of streaming live video would be more open and scalable, as there would be no limit to the number of connections that could be served.

Livepeer项目概述](https://github.com/livepeer/wiki/wiki/Project-Overview) 提供了一个很好的介绍当前视频在互联网上的状态。该白皮书将主要集中于Live对等的加密经济协议细节，而不是业务案例，但总而言之，该概述描述了以快速、集中和昂贵的方式增长的直播流的当前状态。另一方面，完全分散的P2P解决方案，其中节点贡献自己的计算和带宽的流式直播视频服务将更加开放和可扩展性，因为将没有限制的数量，可以提供的连接。

This technology is certainly available to a certain extent, but to date there has been no incentive to get users to run nodes that provide this functionality, nor has there been proper funding for the development of an open protocol that can facilitate this in a way that benefits the entire internet rather than one centralized company. However, with the recent emergence of crypto token powered protocols [[2, 3](#references)], there is now an opportunity to simultaneously incentivize users to contribute computation and bandwidth towards live video broadcast, in a way that aligns with financing the development of an open media server solution capable of delivering live streamed video according to all the latest standards and formats required to reach the full range of devices. Additionally, the economic actions traditionally seen as a result of token powered protocols indicate that the cost to the broadcaster in order to use the Livepeer network could be cheaper than the cost of any centralized solution.

当然，这种技术在一定程度上是可用的，但到目前为止，还没有激励用户运行提供此功能的节点，也没有合适的资金来开发开放协议，这可以有利于整个因特网的发展。而不是一个集中的公司。然而，随着最近出现的密码令牌供电协议 [[2, 3](#references)]，现在有机会同时激励用户向实时视频广播贡献计算和带宽，这与资助开放媒体服务的发展相一致。ER解决方案能够根据所有最新的标准和格式，以达到全范围的设备提供实时流视频。此外，传统上认为是令牌供电协议的经济行为表明，为了使用Livepeer网络，广播公司的成本可能比任何集中式解决方案的成本便宜。

As the Livepeer technology and protocol are delivered, it will enable users to participate in the following flow:

随着LIVER对等技术和协议的交付，它将使用户能够参与以下流程：

1. Capture a video on your camera, phone, screen, or web cam and send it into the Livepeer network.
2. Nodes running within the network will encode it into all the necessary formats to reach every supported device. Users running these nodes will be incentivized via fees paid by the broadcaster in ETH, and the opportunity to build reputation through the protocol token to earn the right to perform more work in the future.
3. Any user on the network can request to view the stream, and it will automatically be distributed to them in near realtime.

>

1. 在相机、电话、屏幕或网络摄像头上捕获视频并将其发送到Livepeer网络。
1. 在网络中运行的节点将把它编码成所有必需的格式，以达到每一个支持的设备。运行这些节点的用户将通过ETH广播支付的费用来激励，并有机会通过协议令牌来建立声誉，以赚取将来执行更多工作的权利。
1. 网络上的任何用户都可以请求查看流，并且它将在近实时地自动分发给它们。


<img src="https://s3.amazonaws.com/livepeerorg/LPExample.png" alt="Livepeer Network Example" style="width: 750px">

### The Live Video Stack 直播视频栈

The technology stack for broadcasting live video has evolved over many years and contains many layers. Broadcasters need to capture video at the source, interface with a media server to process and transcode the video into many different formats, distribute the video across a network, and then allow the video to be played in high perceived quality by the end consumer. There are also economic questions that are introduced when one thinks through this stack, such as whether it should be the broadcaster or consumer who should be paying for the bandwidth to transfer the video.

广播直播视频的技术栈已经发展了多年，包含了很多层。广播公司需要在视频源处捕获视频，与媒体服务器接口，以处理和将视频转换成多种不同的格式，通过网络分发视频，然后允许终端消费者以高感知质量播放视频。当人们思考这个堆栈时，也会出现一些经济问题，比如应该是广播员还是消费者，他们应该支付带宽来传输视频。

A typical live streaming platform today needs to support RTMP, HLS, Mpeg-Dash video formats in H.264 and VP8 codec. New codecs like H.265/HEVC, VP9, and AV1 will become more popular in the near future as consumers become more accustomed to higher video quality.  For HLS alone, [Apple suggests](https://developer.apple.com/library/content/documentation/General/Reference/HLSAuthoringSpec/Requirements.html#//apple_ref/doc/uid/TP40016596-CH2-SW1) bitrates from 145kb/s all the way up to 7800kb/s, in order to serve the different types of devices under different conditions. All of this adds a significant amount of complexity and cost to live video broadcasting.

一个典型的实时流媒体平台需要支持H.264和VP8编解码器中的RTMP、HLS、MPEG DASH视频格式。新的编解码器如H.265/HEVC、VP9和AV1将在不久的将来变得更加流行，因为消费者变得更习惯于更高的视频质量。对于单独的HLS，[苹果建议](https://developer.apple.com/library/content/documentation/General/Reference/HLSAuthoringSpec/Requirements.html#//apple_ref/doc/uid/TP40016596-CH2-SW1) 比特率从145kb/s一直到7800 kb/s，以便服务于不同条件下不同类型的设备。条件。所有这些都为直播视频增加了大量的复杂性和成本。

The existing decentralized development stack (web3) contains solutions for some of the layers required for a live video platform, like file transfer and payments, but currently there are no solutions for the capture and interface, transcoding and processing, and serving layers of live video. For this, Livepeer introduces the [Livepeer Media Server (LPMS)](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server) - an open source implementation of a media server which provides all of the live video specific functionality necessary for DApp developers and existing broadcasters to build live functionality into their applications. [Read more about it here](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server).

现有的去中心化开发堆栈（Web3）包含了对直播视频平台所需的一些层的解决方案，如文件传输和支付，但目前还没有解决方案，用于捕获和接口、转码和处理，以及现场视频的服务层。为此，Livepeer 介绍了 [Livepeer Media Server (LPMS)](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server) —— 一个媒体服务器的开源实现，它提供了DAPP开发者和现有广播者所需的所有直播视频特定功能。将活的功能应用到他们的应用程序中。[在这里阅读更多关于它](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server)。

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
- 节点被授予对网络贡献价值的实际工作，而不是为了试图不公平地获得令牌分配而伪造的工作？

The Livepeer protocol is designed to address both the verification of work and the prevention of fake work, while also offering solutions for automatic scalability of the network and baked in governance for protocol evolution over time.

Livepeer协议被设计用于解决工作的验证和预防伪造工作，同时也为网络的自动可扩展性提供解决方案，并且随着时间的推移实现协议演进的治理。

### Video Segments 视频片段

The core unit of media within Livepeer is what we will call a `segment`. A segment is a time sliced chunk of multiplexed audio and video of time length `t`. Every segment in the Livepeer network is unique, and contains the cryptographic evidence to verify that the broadcaster intended this specific data for this specific segment. Each stream is made up of many consecutive segments, each containing a sequence number identifying their proper ordering. A segment contains the following fields:

Livepeer中的媒体的核心单元是我们将称为 `segment`”的部分。段是时间长度`t`的多路复用音频和视频的时间切片块。Livepeer网络中的每个段都是唯一的，并且包含加密证据，以验证广播公司为这个特定的段打算这个特定的数据。每个流由许多连续的段组成，每个段包含一个序列号来标识它们的正确排序。一个段包含以下字段：

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
| **DataHash** | 数据有效载荷的散列。 |
| **BroadcasterSignature** | 来自 `Priv(StreamID, SequenceNumber, hash(StreamID, SequenceNumber, DataHash))` 的一个签名，它可以用来证明和验证广播者声称这是这个独特片段的真实数据。 |


The Livepeer protocol generally uses segments as the unit of work for transcoding, distribution, and payments.

Livepeer协议通常使用段作为转码、分发和支付的工作单元。

### Livepeer Token

The Livepeer Token (LPT) is the protocol token of the Livepeer network. But it is not the medium of exchange token. Broadcasters use Ethereum's Ether (ETH) to broadcast video on the network. Nodes who contribute processing and bandwidth earn ETH in the form of fees from broadcasters. LPT is a staking token that participants who want to perform work on the network stake in order to coordinate how work gets distributed on the network, and to provide security that the work will get done honestly and correctly. LPT has the following purposes:

Livepeer Token（LPT）是Livepeer网络的协议令牌。但它不是交换令牌的媒介。广播者使用以太币（ETH）在网络上播放视频。贡献处理和带宽的节点从广播者的收费形式获得ETH。LPT是一个标记Token，参与者想要在网络上执行工作，以协调工作如何分布在网络上，并提供工作将得到诚实和正确地完成的安全性。LPT有以下目的：

- It serves as a bonding mechanism in a delegated proof of stake system, in which stake is delegated towards transcoders (or validators) who participate in the protocol to transcode video and validate work. The token, and potential slashing that occurs due to protocol violation, is necessary in order to secure the network against a number of attacks. More below.
- It routes work through the network in proportion to the amount of staked and delegated token, essentially serving as a coordination mechanism.
- It is a unit of account that is specific to the Livepeer ecosystem, which forms the basis of a SectorCoin concept, applicable to additional functionality to be introduced in the future [[4](#references)]. Services such as DVR, closed captioning, ad insertion/monetization, and analytics can all plug into the Livepeer ecosystem and potentially make use of the security provided by staking LPT.
>
- 它作为一个结合机制，在一个委派证明的股份系统，其中的股权委托给转码者（或验证者）谁参与协议转码视频和验证工作。Token和由于协议违反而发生的潜在削减是必要的，以确保网络免受多个攻击。下面更多。
- 它通过与赌注和委托Token的数量成比例地通过网络进行工作，本质上是一种协调机制。
- 它是一个特定的账户单位，它形成了一个部门货币概念的基础，适用于未来将要引入的附加功能 [[4](#references)]。诸如DVR、封闭字幕、广告插入/货币化和分析等服务都可以插入到Livepeer生态系统中，并且潜在地利用STP LPT提供的安全性。

An initial allocation of Livepeer Token will be distributed so that stakeholders can fulfill various roles in, and use the network, and then additional token will be issued according to algorithmically programmed issuance over time. See the [Token Distribution](#token-distribution) section.

Livepeer Token的初始分配将被分配，以便利益相关者可以在网络中使用各种角色，然后根据算法编程的发布随着时间的推移发出附加令牌。参见[Token 分布](#token-distribution) 部分。

Following the conventions of Ethereum and many popular ERC20 tokens [[16](#references)], LPT will be divisible by 10 ^ 18, with larger denominations such as the LPT itself intended to be used for user level transactions such as staking, and smaller denominations intended to be used for protocol accounting.

遵循以太坊和许多流行的ERC20令牌 [[16](#references)] 的约定，LPT可以被10 ^ 18整除，更大的面值，比如LPT本身打算用于用户级别的交易， 旨在用于协议会计的较小面值。

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
| **广播者** | Livepeer节点发布原始流。 |
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

*从广播者到转换器的部分，最终流向消费者。转码器确保他们有签名和证明工作参与工作验证程序。

**Note on Transcoders:** Transcoders play the most critical role in the Livepeer ecosystem. They are the ones who are taking an input stream and converting it into many different formats in a timely manner for low latency distribution. As such they benefit from high availability, efficient, powerful hardware (potentially with GPU accelerated transcoding), high bandwidth connections, and solid DevOps practices. Transcoders should churn far less than other network participants, as when they take on the job of transcoding a stream, it’s less than ideal if they drop off the network. While the network can scale to support many participants playing the role of transcoder (and earning the requisite token allocations), this is a special role that’s delegated from most network participants, in order to ensure that a reliable network that provides value to broadcasters is maintained. More below on this delegation.

**关于转码器的注释：** 转换器在Livepeer生态系统中扮演最关键的角色。他们是谁采取的输入流，并将其转换成许多不同的格式，及时为低延迟分布。因此，它们受益于高可用性、高效、强大的硬件（潜在地具有GPU加速的转码）、高带宽连接和坚实的DevOps实践。转码器应该比其他网络参与者少得多，因为当他们从事对流进行转码的工作时，如果它们掉在网络上就不太理想了。虽然网络可以缩放以支持许多参与者扮演代码转换器的角色（并且赚取必要的令牌分配），但这是从大多数网络参与者委派的特殊角色，以确保为广播提供价值的可靠网络是维持的。更多关于这个代表团。

### Consensus 共识

Livepeer has a two layer consensus system. The LPT ledger and transactions are secured by the underlying blockchain, such as Ethereum. Any transfer of the LPT token or any transaction in the system can be considered to have been confirmed with the same security as the underlying proof of work or proof of stake blockchain. The second layer however, dictates the distribution of newly generated LPT. This is governed by the Livepeer Smart Contract, and participation in the protocol by various actors. While there is no consensus required per say, in terms of acceptance and validation of previous blocks, the protocol defines rules for participation and conditions upon which actors will be penalized (slashed) for failing to fulfill their role.

Livepeer有两层共识系统。LPT分类帐和交易由基础链链（如Ethereum）担保。LPT令牌或系统中的任何交易的任何转移都可以被认为是与基础的工作证明或股权链的证明相同的安全性。然而，第二层决定新生成的LPT的分布。这是由Livepeer智能合约管理的，并且由不同的参与者参与协议。虽然没有表示一致的要求，在接受和验证以前的块，该协议定义的参与规则和条件下，演员将受到惩罚（削减）未能履行自己的角色。

This second level of consensus governing the newly generated token is based upon Delegated Proof of Stake (DPOS), as inspired by systems like Bitshares, Steem, Tendermint, and Casper [[5, 9, 10, 11](#references)]. The role of validators in the network is played by Transcoders. Any user can delegate their stake towards a transcoder, who then needs to perform transcoding jobs in the network, participate in the work verification protocol, and invoke functions on chain at specific intervals to validate this work. The protocol will distribute fees and newly generated token, and it will slash the stake of badly behaved actors. The validation result will be recorded on-chain via Truebit after it performs the validation, so there will be no room for disputes between the broadcaster and the transcoder.

第二层次的共识管理新生成的令牌是基于委托的股份证明（DPOS），灵感来自于系统，如Bitshares，Steem，Tendermint，和Casper [[5, 9, 10, 11](#references)]。验证器在网络中的角色由转码器来扮演。任何用户都可以将他们的股份委托给代码转换器，后者需要在网络中执行代码转换作业，参与工作验证协议，并在特定的时间间隔调用链上的函数来验证这项工作。该协议将分发费用和新生成的token，并将削减行为不良的角色的股份。验证结果将在TwiteBIT完成验证之后通过Truebit记录在链上，因此广播和转码器之间不会有争执的余地。

### Bonding + Delegation 绑定+授权

In Livepeer, in order to indicate stake in the network, nodes must bond some amount of their LPT. They do this through the `Bond()` transaction, which will tie up their stake in the smart contract until they `Unbond()`, at which point they will enter an unbonding state which will last for `UnbondingPeriod` time. Upon completion of the `UnbondingPeriod` they can then withdraw their LPT.

在Livepeer，为了表示网络中的股份，节点必须结合一定数量的LPT。他们通过`Bond()`交易来完成交易，这将把他们在智能合约中的股份绑在一起，直到他们 `Unbond()`，在这一点上，他们将进入一个非绑定状态，该状态将持续到`UnbondingPeriod`。在完成 `UnbondingPeriod` 之后，他们可以撤回其LPT。

The bonded amount is used to delegate stake towards a Transcoder. The network supports `N` active transcoders at any one time, which is a moveable network parameter. Any node can indicate that it wishes to be a Transcoder with a `Transcoder()` transaction, and the protocol will select the `N` transcoders with the most cumulative stake (their own + delegated from other nodes) at the start of each round, along with one random transcoder from the waitlist.

键合量用于将赌注委托给转换器。该网络在任何时候支持`N`活动转码器，这是一个可移动的网络参数。任何节点都可以指示它希望成为具有 `Transcoder()` 事务的转码器，并且该协议将在每个回合开始时选择具有最多累积的份额（其自身+来自其他节点的委托）的 `N` 转码器，以及来自等待列表的一个随机转码器。

Newly generated token in Livepeer is distributed to bonded nodes in relative proportion to the amount of work that they have bonded (minus fees), as long as they’ve delegated towards transcoding nodes that behave according to the protocol. Bonds can be slashed (reduced by a certain percentage) if the nodes that they’ve delegated towards do not behave and violate one of the slashing conditions. Nodes who have bonded and delegated towards a Transcoder also receive a portion of the fees that the Transcoder generates through transcoding jobs on the network. In essence, nodes who perform work, earn the fees that broadcasters paid for that work.

在Livepeer中，新生成的token与绑定的节点的数量成比例（减去费用），只要它们被委托给根据协议运行的转码节点。如果他们委托的节点不遵守和违反一个削减条件，则可以削减债券（减少一定百分比）。向转换器绑定和委托的节点还接收转换器通过网络上的转码作业生成的部分费用。本质上，执行工作的节点赚取广播者为该工作支付的费用。

Going forward, when this document uses the term "delegator", it is referring to bonded nodes who have delegated their stake towards a transcoder candidate, instead of delegating it towards themselves as a transcoder.

展望未来，当该文档使用术语“委托人”时，它指的是绑定节点将他们的股份委托给转换器候选，而不是将其作为转码器委托给他们自己。

In summary, participants choose to bond their stake for the following reasons:

综上所述，参与者选择结合他们的股份,理由如下：

- Participate in delegating towards effective transcoders who will provide great service to the network, ensuring its value to broadcasters.
- Build reputation and future-work allocation in form of allocated token in proportion to stake.
- Earn fees generated from transcoders.
- They may wish to be a Transcoder.

- 参与向有效的转换器的委派，为网络提供巨大的服务，确保其对广播电台的价值。
- 以分配比例与持股比例建立声誉和未来的工作分配。
- 赚取来自转换器生辰的费用。
- 他们可能希望成为转换器。

### Transcoder() Transaction 事务

A node indicates their willingness to be a transcoder by submitting a `Transcoder()` transaction, which publicizes the following three properties:

一个节点通过提交一个`Transcoder()` 事务表明他们愿意成为一个转换器，它将公布以下三个属性：

- `PricePerSegment`: the lowest price they are willing to accept to transcode a segment of video
- `BlockRewardCut`: The % of the block reward that bonded nodes will pay them for the service of transcoding. (Example 2%. If a bonded node were to receive 100 LPT in block reward, then 2 LPT to the transcoder).
- `FeeShare`: The % of the fees from broadcasting jobs that the transcoder is willing to share with the bonded nodes who delegate towards it. (Example 25%. If a transcoder were to receive 100 ETH in fees, they would pay 25 ETH to the bonded nodes).
>
- 'PricePerSegment'：他们愿意接受的最低价格来转码一段视频
- `BlockRewardCut`：保税节点为转码服务支付的块奖励的百分比。 （例2％，如果一个绑定节点在块奖励中接收到100个LPT，则向转码器接收2个LPT）。
- 'FeeShare'：转换器愿意与委托给它的保税节点共享广播作业的费用百分比。 （例如25％。如果一个代码转换器收到100个ETH的费用，他们将向保税节点支付25个ETH）。

The Transcoder can update their availability and information up until `RoundLockAmount` time before the next transcoding round. This is offered as a % of the round. (Example 10% == 2.4 hours. They can change this information until 2.4 hours before the next transcoding round which lasts for `RoundLength` 1 day). This gives bonded nodes the chance to review the fee splits and token reward splits relative to other transcoders, as well as anticipated fees based upon the rate they're charging and network demand, and move their delegated stake if they wish. At the start of a transcoding round (triggered by a call to the `InitializeRound()` transaction), the active transcoders for that round are determined based upon the total stake delegated towards each transcoder, and stakes and rates are locked in for the duration of that round.

转换器可以更新它们的可用性和信息直到下一轮代码转换之前的`RoundLockAmount`时间。这是作为回合的一%。（例10% == 2.4 小时）。他们可以改变这个信息，直到下一个代码转换回合持续1天之前的2.4小时。这使得保税节点有机会审查费用分裂和token奖励分裂相对于其他转码器，以及预期费用的基础上，他们的收费和网络需求，并移动其委派的股份，如果他们希望。在代码转换回合开始时（由对`InitializeRound()`的事务调用）触发，该回合的活动转码器基于委托给每个转码器的总赌注确定，并且在该回合持续期间锁定和速率被锁定。

There is one change that is allowed during the `RoundLockPeriod`: The lowest offered price/segment for any of the candidate transcoders is locked in and can't be moved, but other transcoder candidates can adjust their price/segment downwards. This allows them to match the lowest offered price on the network if they wish in order to guarantee their stake-weighted share of work on the network. They are not allowed to move their offered price upwards during this period.

在`RoundLockPeriod` 期间允许有一个变化：对于任何候选的转码器，最低的提供价格/段被锁定并且不能被移动，但是其他转换器候选可以向下调整它们的价格/段。这使他们能够匹配网络上最低的报价，如果他们希望，以保证他们的股权加权份额的工作在网络上。在这段时间内，他们不允许将报价上调。

Here is an example state of Transcoder options that a delegator can review when deciding whom to delegate towards.

这里是一个转换器选项的示例状态，在决定委托给谁时，委托人可以查看该选项。

| Transcoder ID | PricePerSegment | BlockRewardCut | FeeShare |
|----|----|----|----|
| 1 | 22 wei | 1% | 25% |
| 2 | 30 wei | 2% | 40% |
| 3 | 10 wei | 4% | 1% |
| ... | ... | ... | ... |
| N | 14 wei | 0% | 2% |

*Note on price: In this document we list price/segment. In reality, Livepeer plans to use a gas accounting inspired model where there is a notion of units of gas required for certain job parameters of a segment such as bitrate, encoding, frame size, etc. Price/segment is a stand in, where the incentives are the same, but in reality they’ll likely be communicating price/gas.*

*注意价格：在这份文件中，我们列出价格/段。在现实中，Livepeer计划使用一个gas会计启发模型，其中有一个概念的单位的gas所需的某些工作参数的一个环节，如比特率，编码，帧大小等。价格/段是一个站在那里，激励是相同的，但实际上，他们很可能是沟通中的价格/gas*

### Broadcast + Transcoding Job 广播+转码作业

Transcoders who are open for business on the network, throw their hat into the ring for transcoding work by submitting a `TranscodeAvailability()` transaction. This indicates their availability and places them into a pool of transcoders available to take a newly submitted job.

在网络上开放业务的转换器通过提交一个`TranscodeAvailability()`事务将他们的帽子投入到代码转换工作中。这表明它们的可用性，并将它们放入一个可用新提交的工作的转换器池中。

When a broadcaster submits their stream into the Livepeer network it is given a `StreamID`. This serves as both a unique identifier, and it also contains the origin node address so that nodes know how to request and route requests to consume this stream towards the origin. The stream contains many consecutive `Segments`, as described in the [Video Segments](#video-segments) section. If the broadcaster would like the network to take care of transcoding their stream into all the formats and bitrates necessary to reach every user on every device, then the first step is submitting a transcoding job transaction on chain. Jobs are given a unique ID as well, and the input data to job consists of:

当广播员将其流提交到Livepeer网络时，它被赋予`StreamID`。这既充当唯一标识符，又包含源节点地址，以便节点知道如何请求和路由请求，以将该流消耗到原点。该流包含许多连续的`Segments`，如[Video Segments](#video-segments) 中所描述的。如果广播者希望网络将其流转换成所有的格式和比特率，以达到每个设备上的每个用户，那么第一步就是提交一个链上的转码作业事务。作业也被赋予唯一的ID，并且作业的输入数据包括：

`Job(StreamID, TranscodingOptions, PricePerSegment)`

The `TranscodingOptions` define the output bitrates, formats, encodings, etc, and the `PricePerSegment` lists the price that the broadcaster will offer.

 `TranscodingOptions` 定义了输出比特率、格式、编码等，并且`PricePerSegment`列出了广播公司将提供的价格。

As soon as this transaction is mined, the next blockhash will be used to pseudo-randomly determine the transcoder selected for this job. All transcoders with a price that’s lower than or equal to the price offered will be considered, and the blockhash modulus the number of candidate transcoders (weighted by their stakes) will determine the index of the selected transcoder.

一旦该事务被挖掘，下一个块哈希将被用于伪随机地确定为该作业选择的转换器。所有价格低于或等于所提供价格的转码器将被考虑，而块哈希模数的候选转码器（由它们的赌注加权）将决定所选代码转换器的索引。

At this point the broadcaster can begin streaming video segments towards the transcoder, and they’ll participate in the following protocol. The protocol also makes use of a persistent storage solution, for example Swarm, as part of the work verification process.

在这一点上，广播者可以开始向转码器流视频片段，并且他们将参与以下协议。该协议还使用持久性存储解决方案，例如Swarm，作为工作验证过程的一部分。

#### Preprocessing 预处理

1.  **Broadcaster**  -> **Livepeer Smart Contract**: submits a deposit on chain to cover the cost of the full transcoding job. This can be refilled later at any point, but the Transcoder may stop work if the deposit runs out as they gradually cash in for work done.

1。**广播者** -> **Livepeer智能合约**：在链上提交一个定金以覆盖完整的转码作业的费用。这可以在以后的任何时候重新填写，但是如果存款在完成工作时逐渐用完，转码器就可以停止工作。

#### The Job 作业

2. **Broadcaster** -> **Livepeer Smart Contract**: Job(streamID, options, price/segment)
    - Creates the job request on chain and places some ETH in escrow to pay for the work.
3. The protocol can use the next block hash to deterministically select the correct Transcoder for this job.
4. **Transcoder** -> **Broadcaster**: send output streamID and receipt that the job is accepted.
5. **Broadcaster** -> **Transcoder**: send stream segments, which contain signatures verifying the input data.
7. **Transcoder** performs transcoding and makes new output stream available on network
9. **Transcoder**: Store a transcode receipt for each segment of transcoding work. A transcode receipt has the following fields.

| Transcode Receipt Field | Description |
|-------|------------|
| **StreamID** | Identifies the origin node and stream that this segment belongs to. | 
| **Sequence Number** | The sequential order that this segment belongs in the original stream. |
| **Input Data hash** | The hash of the input segment data payload. |
| **Transcoded Data hash** | The hash of the output data after transcoding this segment. |
| **Broadcaster segment signature** | A signature from the broadcaster of Priv(StreamID, Seq#, Dhash) which can be used to attest and verify that the broadcaster claims this to be the true data for this unique segment. |
| **Transcoder segment signature** | A signature of all of the above fields from the transcoder attesting to the claim that this specific output transcoding was performed on this specific input. |

Whenever the transcoder observes that they are no longer receiving segments, they can call `ClaimWork()` to claim their work.

#### End Job

10. **Transcoder** -> **Livepeer Smart Contract**: Call `ClaimWork(JobID, StartSegmentSeq#, EndSegmentSeq#, MerkleRoot)`. Transcoder is claiming on chain they have performed work on the claimed segment range, with a merkle root of all of the transcode receipt data to commit to the content of these encoded segments.
11. Wait for this transaction to be mined, and observe the next blockhash. The protocol can then determine which segments will be verified based upon the `VerificationRate`.
12. **Transcoder** -> **Swarm**: Write input data payloads for the segments that will be challenged via verification, using SWEAR params to ensure the data will be there long enough for verification (`VerificationPeriod` time).
13. **Transcoder** -> **Livepeer Smart Contract**: Provide transcode claims on chain for each segment that needs to be verified, along with merkle proofs for the receipts for each segment in the transcode claims. The smart contract can verify the signatures from Broadcaster and **Transcoder** to ensure all data necessary is available to conduct verification, and can verify the merkle proofs against the committed merkle root from `ClaimWork()`.
14.  **Transcoder** -> **Truebit**: `Verify()`. This is an onchain call to the Truebit smart contract, where the Transcoder provides the Swarm input hash for the challenged segment. (More on verification in the following section)
15. **Truebit** -> **Livepeer Smart Contract**:  The result of the job is written on chain. This is compared to the transcoding claim result that the Transcoder provided.
16.  **Livepeer Smart Contract**: at this point the Livepeer smart contract has all the information it needs to determine if the Transcoder’s work is verified.
    - If verified correct, then use as input to token allocation algorithm and release of escrowed fees.
    - If incorrect, then Transcoder and its stakers get slashed `FailedVerificationSlashAmount` and the Broadcaster is refunded.

The Broadcaster can stop sending segments at any point, which effectively is an `EndJob()`.

At this point the transcoding has been performed, proof of the work has been claimed on the chain, and failure or success of the verification of the work has been reported. All the info is on chain to determine allocation of fees and token allocations to transcoders and delegators, or slashing in the case of failed verification. Let’s take a look at how work is actually verified.

### Verification of Work

In order to allocate fees to transcoders who claim that they have performed a transcoding job, it’s necessary that the protocol can determine that the job was actually performed correctly with high probability. For this, Livepeer extends the research of, and makes use of, the [Truebit Protocol](http://truebit.io) [[6](#references)].

Truebit works by having one participant (the solver) perform the actual work for the fee, in this case transcoding, and then having additional participants (verifiers) verify the work in order to detect mistakes, errors, or cheating. The task is broken down into very small steps, and the verifiers check the work of the solver to find the first step that differs from what they expected it to be. Then, only this one very small step needs to be played out on chain by a smart contract (judge), who can tell which party did the work correctly. The economic incentives, including forced errors to incentivize checking on the part of verifiers, ensure that it is not profitable to cheat or challenge incorrectly, but it is profitable to play the role of checking the work.

The downside of this protocol is that it costs between 5x-50x the cost of the original work in order to verify all work. Livepeer uses Truebit as a black box to verify segments, but it gets around having to pay this very high verification tax by only verifying a small percentage of segments randomly, and using slashing in the case of failed verifications. The `VerificationRate` set within Livepeer determines how frequently a specific segment is to be selected for challenge within Truebit, and the randomness of a future block hash after the work has been committed to the blockchain, determines which segments specifically are selected.

If work is committed via an `ClaimWork()` call in block `N`, then

If `Sha3(N, BlockHash(N), Seg#) % VerificationRate == 0` then the segment # must be verified.

The Transcoder provides Transcode Claims on chain for the candidate segments by invoking the `Verify()` transaction. The Livepeer Smart Contract can verify the authenticity of these claims using the internal signatures and provided merkle proofs, and then invoke a call to Truebit to verify only these segments.

Truebit solvers and verifiers access the input data for a segment from a persistent content addressed storage system, such as Swarm. The Transcoder is responsible for verifying that the segment data is available in Swarm, and can optionally look for receipts from the SWEAR protocol [[5](#references)] guaranteeing persistence for a certain period of time, which is long enough for Truebit to play out. Additionally, they can take it upon themselves to run a Swarm node ensuring that the data is available to Truebit verification. If they have reason to believe that data is not available in Swarm, they can provide it, or just call `ClaimWork()` on the previously available data.

Truebit will write the results of the computation (succeeded or failed) back to the Livepeer Smart Contract, which can then be used in the reward and slashing calculations within the protocol. A transcoding node can not predict in advance which segments will be verified, and the following penalties will be felt in the case of cheating or failing to transcode correctly:

- `FailedVerificationSlashAmount` will be slashed if they fail a verification from Truebit.
- `MissedVerificationSlashAmount` will be slashed if they fail to provide transcode claims and invoke Truebit on segments they were required to do so.
- Lost fee from the broadcaster.
- Not only will the Transcoder be slashed, but all their delegators will be slashed as well. They will take this account into their decision of who to delegate towards, and the Transcoder could lose the lucrative job they hold.

It is important that it be more profitable to simply stake LPT towards a valid, honestly performing transcoder, than it can be to cheat and take slashing penalties while still collecting fees and token allocations for dishonest work. Careful selection of the slashing params and verification rate can ensure this.

#### A Note On Truebit

*While the protocol makes use of Truebit in order to provide fully trustless verification of work, it may be necessary in practice to use available solutions that provide verification without the degree of trustlessness that Truebit can offer while Truebit is still under development and testing. Some options, ordered by degree of trustlessness, include:*

*1. Livepeer API Based Oracle - Trust Livepeer to verify computation. Very centralized, not ideal for anything beyond testing.*  
*2. Oraclize Computation Service - Trust a company who provides proofs of computation and who's entire reputation relies upon putting external data on chain with proofs that it wasn't tampered with.*  
*3. Secure hardware enclaves - Services like Intel SGX or TownCrier provide trusted computing environments. Trust that their hardware implementation is correct and secure. This can be decentralized and audited.*


### Token Generation  Token的生产

Livepeer is inflationary in that new tokens will be generated and allocated over time according to the schedule communicated below in [Token Distribution](#token-distribution). If all roles in Livepeer behave according to the protocol, then newly generated tokens will be allocated to users in proportion to their bonded stake (minus fees). Transcoders have the role of calling the `Reward()` function in order to trigger the new token allocation or slashing which can be computed from all data available on chain.

Each transcoder will be required to call `Reward()` once per round.

- Ensure that an active Transcoder is calling `Reward()`.
- Ensure that the Transcoder has not called `Reward()` yet in this round.
- Compute the number of token to mint based upon the `InflationRate`. Mint this many token.
- Calculate the Transcoder's cut based upon their `BlockRewardCut`.
- Distribute this into the Transcoder's bonded stake.
- Distribute the remainder into the delegators reward pool.
- Update the bonded amount of token to this Transcoder.

Failure to invoke `Reward()` results in the direct consequence of losing a portion of token allocations, and showing up as a ding on one’s Transcoder reputation when it comes to being elected by Delegators for the role.

### Slashing

As previously mentioned, the conditions for slashing are:

- Failing a verification
- Failing to invoke verification when required to do so
- Not performing a proportional share of the required work within the platform based upon delegated stake

One of the benefits of building within the Ethereum ecosystem are the network effect benefits you receive from being able to build on top of other protocols such as Truebit and Swarm/SWEAR. Unfortunately, with reliance on these external systems, which themselves have external dependencies and incentives, it’s possible that a flaw or weakness in one of those protocols could result in slashing within Livepeer.

For example, if a Truebit verification job sat in their queue for a long period of time without any solver or verifier claiming it, Livepeer would fail to see the result of that verification in time before `Reward()` was called. Or if the Swarm network suffered a partition and couldn’t propagate the file to the Truebit verifier in time, then this could also create an issue.

These risks can be mitigated by incentivizing these roles to be played in house by participants in the Livepeer protocol, who may find it in their best interest to serve as Truebit verifiers or Swarm nodes. But there’s also another approach which is introducing the concept of probability thresholds on the slashing parameters. Optional protocol variables such as `VerificationFailureThreshold` could be set to indicate that as long as the node passes 99% of verifications they won’t be slashed for example. This will remain a further area of research to be worked out prior to network deployment.

The failure to invoke verification slashing condition can be checked and invoked by any Livepeer protocol participant. There is a `FinderFee` which specifies the percent of the slash amount which the finder will receive as a reward for successfully invoking this slashing condition.

The remainder of the slashed funds will enter the `CommonPool`, which can be burned or allocated to common uses such as further ecosystem development, according to the governance mechanism of the protocol.

### Token Distribution

As a token that represents the ability to participate and perform work in the network through a DPoS staking algorithm, the initial Livepeer token distribution will follow the patterns of other DPoS systems which require a widely distributed genesis state.

An initial allocation of the token will be distributed to the community at genesis and over the early stages of the network. Receipients can use it to stake into the role of Transcoder or Delegator. A portion will be allocated to groups who contributed prior work and money towards the protocol before the genesis, and a portion will be endowed for the long term development of the core project.

At the launch of the network, token issuance will continue according to an inflationary schedule with token being generated at `InflationRate` per round relative to the outstanding float of token. As token is issued in proportion to stake of all bonded participants in the protocol, it serves to incentivize active participation. Participants are "protected" from this inflation, due to earning their proportional share. It is only inactive participants who are sitting on token without bonding it for participation, who will see their proportional network ownership dilluted by this inflation.

The initial target for `InflationRate` will be set such that it aims to incentivize approximately `ParticipationRate` of the LPT to be bonded and actively participating [[19](#references)). For example, if `ParticipationRate` is 50% then incentives will exist to have half the oustanding token bonded. The inflation rate will move algorithmically each round to incent the participation target. A higher inflation rate would incent more token to be bonded, and a lower rate would lead to more people choosing liquidity rather than participation. It's this liquidity preference vs network ownership percentage tradeoff which should find equilibrium due to a number of economic factors in the network.

### Governance

The role of governance within the Livepeer protocol is intended to be three fold:

1. Determine the burning or appropriation of common funds which were slashed from misbehaving nodes.
2. Adjust network parameters to ensure a healthy, thriving network which is valuable to broadcasters.
3. Invoke proposed protocol updates in a decentralized fashion.

Many of the network parameters referenced in this document such as `UnbondingPeriod`, `RoundLength`, `ParticipationRate`, and `VerificationRate` are adjustable. Proposals for adjustments to these parameters can be submitted, and the governance process, including voting by transcoders in proportion to their delegated stake, will determine adoption of these changes automatically within the protocol. The detailed spec for governance is left for another document. [See more here](https://github.com/livepeer/wiki/wiki/Governance). 

## Attacks

This section contains a survey of the various ways that malicious actors may try to attack the Livepeer network. We use a rational attacker model in which the attacker makes decisions based upon their own economic self interest. A number of attacks are mitigated via it being unprofitable to conduct such attacks, but we also strive to ensure that at the worst the network suffers decrease of efficiency in the case of a sustained unprofitable attack, and doesn't suffer a failure.

### Consensus Attacks

As mentioned previously, consensus in the Livepeer ecosystem is provided by the underlying blockchain platform (Ethereum for example). 51% attacks, double spends of Livepeer Token, and forks of the network would require the same resources and cost-of-attack as Ethereum itself.

Livepeer is a staked based protocol, and while Transcoders have the role of participating in the work verification process and the token reward distribution process, they actually do not have the role of validating or accepting other Transcoders' work. There is no concept of a chain, nor is there validation of previous blocks. There simply exists the economic incentives to verify one's own work and distribute one's own portion of token allocations when it is one's turn. As such, attacks that are seen in a proof of stake protocols such as the Long Range Attack, the Nothing at Stake problem, and The Bribe Attack don't apply, as there is no opportunity to attempt to sign multiple blocks or attempt to create a longer chain from an earlier state. However, one should be aware that as the underlying blockchain migrates to proof of stake, these attacks do threaten to undermine Livepeer if the benefit of carrying them out on Livepeer were to exceed the cost of attack on Ethereum itself.

While relying on the security of the underlying blockchain is nice for prevention of consensus attacks, there still exists a class of quality and efficiency attacks that can harm the Livepeer network.

### DDoS

Denial of Service in Livepeer can go two ways:

1. A Transcoder can try to prevent or slow down a Broadcaster from getting their encoded stream out to the network by accepting a job but refusing to transcode.
2. A Broadcaster can prevent a Transcoder from being able to do the job that they believe they were assigned by refusing to send them segments.

Both attacks have a cost and can be mitigated, with slight annoyance.

In the first case, a Transcoder has to pay to claim their availability on chain. If they are not going to receive a fee because they didn't do the work, then they're throwing ETH away. The Broadcaster can just resubmit the job and be assigned a new Transcoder. One potential option for scalability is that the protocol can identify a number of valid Transcoders in priority order instead of just one, and this way the Broadcaster can just move on without another on chain transaction. Additionally, all stats about accepted jobs and average # of segments transcoded/job, etc, can be calculated from on-chain data, and delegators would use this as input into their decision about whom to delegate towards. Behave poorly and lose your role.

In the case of a Broadcaster preventing a Transcoder from doing work, this is merely a capacity planning calculation. A Transcoding node can maintain records of its capacity for concurrent jobs, likelihood of a job being active/inactive, and ensure that it always believes it will have capacity for the work that it claims. Simply ignoring or calling `EndJob()` on a node that's refusing to send segments hardly hurts the Transcoder.

### Useless or Self Dealing Transcoder

If a Transcoder has enough stake to maintain their position, they could theoretically list a 100% `BlockRewardCut`, 0% `FeeShare`, and charge a high `PricePerSegment` such that they would never have to do any work, yet could collect their token allocation. This is prevented by the `CompetitivenessTolerance` which requires them to contribute some amount of valid work. Additionally, because of the transaction costs of participating in the protocol incurred by Transcoders, it would be more profitable for them to simply stake their token toward a valid Transcoder who was sharing fees with them, than it would be to act as a useless Transcoder who would receive no fees to speak of.

A misbehaving Transcoder who is outputting invalid output would quickly get slashed down to the point of their stake being reduced too low to actually keep their job and receive any work.

### Transcoder Griefing

If a Broadcaster wanted to make the protocol very expensive to operate for a transcoder, it could send transcoders non-consecutive segment numbers. This is because transcoders can claim work for a continuous range of segment numbers in a single transaction, but would have to make many transactions to claim work across random segment number ranges. This can be defended against by the following options:

1. Transcoder calls `EndJob()` and doesn't bother doing the work or attempting to collect the fees. 
2. Protocol implements on chain parsing or better segment claim encoding in order to reduce fees associated with claiming non-consecutive segments in a single call.
3. Simply ignore the segments and never claim the work.

This attack has a high cost to a broadcaster since they must have a deposit and submit jobs on chain in order to even get assigned to a transcoder in the first place. They have the ability to make life annoying for a transcoder and potentially lose efficiency, but not cause damage to the network.

### Chain Reorg

When a broadcaster submits a job to the Livepeer Smart Contract, the protocol uses the current block hash to determine which transcoder will be assigned the job. Reorganizations of the underlying blockchain can cause confusion in this scenario. While this is not "an attack" directly, a transcoder will be valid one second, and then upon reorganization, will no longer be valid. When a reorg is detected the broadcaster can either redirect the stream towards the new valid transcoder, or the protocol can detect uncle blocks that are included in the main chain, and consider a transcoder to be valid if an uncle block within a given threshold would have made them valid. 

## Live Video Distribution ###########################################

This whitepaper has largely focused on the economic incentives and protocol for ensuring proper transcoding of live video, which is necessary to support adaptive bitrate streaming and reach every device. But equally important is the distribution of video throughout the network so that it can be consumed with high quality and low latency. The economics of distribution rely on tit-for-tat bandwidth accounting as popularized by Bittorrent, and extended via protocols like SWAP [[13](#references)]. As a simplification, nodes pay to request a segment of video, and nodes get paid to serve a segment of video. If a node already has a segment and can serve it to multiple requestors, it is profitable. We call this type of node, a Relay node.

Different incentives exist when it comes to bandwidth for nodes playing different roles in the network.

* Consumers may be willing to exchange upstream bandwidth to serve the content to additional Consumers in exchange for being able to consume the video themselves free of charge. See systems like Webtorrent [[14](#references)].
* Broadcasters serve as origin nodes and may want to charge for consumption of the video, or may want to subsidize the cost of bandwidth so that everyone can access their video for free.
* Transcoders and Relay nodes are willing to provide bandwidth in service of distributing video as long as it is profitable. This is similar to the role of traditional CDNs.



With `Segments` as the core unit of data flowing through the network, it is possible to do tit-for-tat bandwidth accounting using ETH as the basis for settlement. We borrow the Chequebook Contract abstraction from Swarm [[6](#references)] as a method of offchain payment passing with on chain settlement. Future developments in the ecosystem including the Raiden Network [[15](#references)] may allow of payment channels to be used for this purpose as well. Since token transfer is native to the protocol, it is also possible to embed pricing associated with content directly into the protocol. A broadcaster can charge for their time or content directly, and nodes will opt into this transfer of value by paying a higher price/segment which will flow back to the broadcaster.

What's important to note is that while bandwidth accounting can be used to make it profitable to run Relay Nodes which just pass video segments around the network to add capacity, a-la a CDN, these nodes are purely incentivized by demand for the content, and not incentivized by new token allocations. In fact, the output of Livepeer can be inserted into a traditional CDN (like Amazon S3, Cloudflare, etc) or decentralized CDN (like IPFS or Swarm). Development of this peer-to-peer protocol for video segment distribution itself will be an ongoing opportunity for optimization and improvement in performance.

Peer-to-peer CDNs have been shown to reduce 80-98% of bandwidth requirements on an origin CDN server [[17](#references)], and the token mechanics seen in decentralized networks can align stakeholders for the development and maintenance of an open version of the proprietary P2P CDNs that exist today. The PPSPP Protocol [[18](#references)] serves as a viable candidate for an open implementation that focuses on delivery of live content.

As non-critical to the cryptoeconomics of the Livepeer protocol, the details are spared from this document, but the interested can [follow along here](https://github.com/livepeer/go-livepeer) with the development, and look for a future document addressing purely the video distribution protocol.

## Use Cases ###########################################

The Livepeer project is concerned with decentralizing one-to-many live video broadcast (multicast). This is the truest form of media distribution, as it allows a broadcaster to connect directly with their audience in a first-hand manner, free from alterations, after-the-fact interpretation, and spin. It gives everyone a platform to have a voice. Existing centralized solutions can suffer from censorship, third party control over user data/relationship/monetization, and inefficient cost structures around payment for the service. Here are some of the logical use cases for applications and services to be built on top of Livepeer.

### Pay-As-You-Go Content Consumption

With a transfer of value transaction baked into the protocol, it is now possible for broadcasters to charge viewers directly for the consumption of their live broadcast, without requiring a credit card, account, or control over user identity via a centralized platform. This has applications in education (pay to attend an online course), events (pay to view a concert or live sporting event), entertainment (pay to watch a gamer or performer's live stream), and many other use cases - all while preserving the privacy of the viewer, and allowing them to pay for only what they consume directly to the broadcaster.

### Auto-scaling Social Video Services

One of the challenges of building consumer video services today is scaling infrastructure to support the demand for the growing number of streams and growing number of consumers as new users are added. A service layer that easily lets developers begin building their video solution on top of the Livepeer Network, which will automatically scale to support any number of streams and viewers as they go, will be a welcome solution to infrastructure developers who would otherwise have to continue provisioning servers, licensing media servers, and efficiently manage resources to handle spikes.

### Uncensorable Live Journalism

Current platforms such as Twitter and Facebook provide amazing live video solutions for reaching a large audience, but they're also the first to get blocked or censored in a variety of political conflict situations. Use of a decentralized network such as Livepeer would render it nearly impossible to prevent the word from getting out as to what is really going on on the ground in realtime.

### Video Enabled DApps

Decentralized apps (DApps) are beginning to emerge, driven largely by the Ethereum ecosystem. However, to date there hasn't been a viable solution for embedding live video within a DApp without using a centralized solution or limiting the number of consuming clients based on the constraints of WebRTC. By introducing Livepeer to the stack, an application can be fully decentralized, yet still contain live video, at scale, to as many users as wish to consume it.

## Summary ###########################################

In summary, the Livepeer protocol incentivizes nodes to contribute their processing and bandwidth to the network in service of transcoding and distributing live video. The verification of work is solved by a scalable extension on top of the Truebit protocol which incentivizes nodes to perform transcoding operations correctly in order to earn their fees and token allocations and preserve their value earning role as a transcoder. The gamification of the network and false work problem is solved via the economics of the delegated proof of stake block reward accounting. It becomes more economically rational to simply stake one's tokens towards a value adding node than to pay fees into the network to be distributed to other delegators when performing work that there wasn't actually real demand for.

The end result is a scalable, pay-as-you-go network for decentralized live video broadcast - a missing layer in the web3 stack that Livepeer seeks to fill.

## Appendix ###########################################

### Livepeer Protocol Parameter Reference

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

### Livepeer Protocol Transaction Types

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

## References ###########################################

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
