# Livepeer Whitepaper

**Protocol and Economic Incentives For a Decentralized Live Video Streaming Network**

Doug Petkanics <doug@livepeer.org>  
Eric Tang <eric@livepeer.org>

## Abstract ###########################################

The Livepeer project aims to deliver a live video streaming network protocol that is fully decentralized, highly scalable, crypto token incentivized, and results in a solution which can serve as the live media layer in the decentralized development (web3) stack. In addition, Livepeer is meant to provide an economically efficient alternative to centralized broadcasting solutions for any existing broadcaster. In this document we describe the Livepeer Protocol - a delegated stake based protocol for incentivizing participants in a live video broadcast network in a game-theoretically secure way. We present solutions for the scalable verification of decentralized work, as well as the prevention of useless work in an attempt to game the token rewards in an inflationary system.

## Table of Contents ###########################################

* [Introduction and Background](#introduction-and-background)
    * [The Live Video Stack](#the-live-video-stack)
* [Livepeer Protocol](#livepeer-protocol)
    * [Video Segments](#video-segments)
    * [Livepeer Token](#livepeer-token)
    * [Protocol Roles](#protocol-roles)
    * [Consensus](#consensus)
    * [Transcoder() Transaction](#transcoder-transaction)
    * [Broadcast + Transcoding Job](#broadcast--transcoding-job)
        * [Preprocessing](#preprocessing)
        * [The Job](#the-job)
        * [End Job](#end-job)
    * [Verification of Work](#verification-of-work)
    * [Token Rewards](#token-rewards)
    * [Slashing](#slashing)
    * [Token Distribution](#token-distribution)
    * [Governance](#governance)
  * [Attacks](#attacks)
    * [Consensus Attacks](#consensus-attacks)
    * [DDoS](#ddos)
    * [Forced Slashing](#forced-slashing)
    * [Useless or Self Dealing Transcoder](#useless-or-self-dealing-transcoder)
    * [Chain Reorg](#chain-reorg)
* [Live Video Distribution](#live-video-distribution)
* [Use Cases](#use-cases)
    * [Pay-As-You-Go Content Consumption](#pay-as-you-go-content-consumption)
    * [Auto-Scaling Social Video Services](#auto-scaling-social-video-services)
    * [Uncensorable Live Journalism](#uncensorable-live-journalism)
    * [Video Enabled DApps](#video-enabled-dapps)
* [Summary](#summary)
* [Appendix](#appendix)
    * [Livepeer Protocol Parameter Reference](#livepeer-protocol-parameter-reference)
    * [Livepeer Protocol Transaction Types](#livepeer-protocol-transaction-types)
* [References](#references)

*Note: This document will continue to be updated along the path to the 1.0 deployment of Livepeer. Check back for changes.*

## Introduction and Background ###########################################

The vision of the decentralized web has begun to be realized over the past couple years with the emergence of networks like [Ethereum](http://ethereum.org) to enable trustless computing, [Swarm](http://swarm-gateways.net/bzz:/theswarm.eth/) and [IPFS/Filecoin](http://ipfs.io) to enable decentralized storage and content distribution, Bitcoin and various token projects to facilitate p2p transfer of value, and decentralized name registries like [Blockstack](http://blockstack.org) and [ENS](http://ens.readthedocs.io/en/latest/introduction.html) to provide human accessible names to content and identities. These elements form the basis for decentralized applications (DApps) to be built in the form of largely static or infrequently updated web or mobile content, but at the moment DApps still lack the ability to include streaming media and data in an open and decentralized way. The goal of the Livepeer project is to decentralize live video broadcast over the internet.

The [Livepeer Project Overview](https://github.com/livepeer/wiki/wiki/Project-Overview) provides a nice introduction to the current state of live video on the internet. This whitepaper will largely focus on the cryptoeconomic protocol details of Livepeer, rather than the business case, but in summary the overview describes the current state of live streaming as growing at a rapid pace, centralized, and expensive. On the other hand, a fully decentralized P2P solution, where nodes contributed their own computation and bandwidth in service of streaming live video would be more open and scalable, as there would be no limit to the number of connections that could be served.

This technology is certainly available to a certain extent, but to date there has been no incentive to get users to run nodes that provide this functionality, nor has there been proper funding for the development of an open protocol that can facilitate this in a way that benefits the entire internet rather than one centralized company. However, with the recent emergence of crypto token powered protocols [[2, 3](#references)], there is now an opportunity to simultaneously incentivize users to contribute computation and bandwidth towards live video broadcast, in a way that aligns with financing the development of an open media server solution capable of delivering live streamed video according to all the latest standards and formats required to reach the full range of devices. Additionally, the economic actions traditionally seen as a result of token powered protocols indicate that the cost to the broadcaster in order to use the Livepeer network could be cheaper than the cost of any centralized solution.

As the Livepeer technology and protocol are delivered, it will enable users to participate in the following flow:

1. Capture a video on your camera, phone, screen, or web cam and send it into the Livepeer network.
2. Nodes running within the network will encode it into all the necessary formats to reach every supported device. Users running these nodes will be incentivized via fees paid by the broadcaster as well as newly minted protocol token.
3. Any user on the network can request to view the stream, and it will automatically be distributed to them in near realtime.

<img src="https://s3.amazonaws.com/livepeerorg/LPExample.png" alt="Livepeer Network Example" style="width: 750px">

### The Live Video Stack

The technology stack for broadcasting live video has evolved over many years and contains many layers. Broadcasters need to capture video at the source, interface with a media server to process and transcode the video into many different formats, distribute the video across a network, and then allow the video to be played in high perceived quality by the end consumer. There are also economic questions that are introduced when one thinks through this stack, such as whether it should be the broadcaster or consumer who should be paying for the bandwidth to transfer the video.

A typical live streaming platform today needs to support RTMP, HLS, Mpeg-Dash video formats in H.264 and VP8 codec. New codecs like H.265/HEVC, VP9, and AV1 will become more popular in the near future as consumers become more accustomed to higher video quality.  For HLS alone, [Apple suggests](https://developer.apple.com/library/content/documentation/General/Reference/HLSAuthoringSpec/Requirements.html#//apple_ref/doc/uid/TP40016596-CH2-SW1) bitrates from 145kb/s all the way up to 7800kb/s, in order to serve the different types of devices under different conditions. All of this adds a significant amount of complexity and cost to live video broadcasting.

The existing decentralized development stack (web3) contains solutions for some of the layers required for a live video platform, like file transfer and payments, but currently there are no solutions for the capture and interface, transcoding and processing, and serving layers of live video. For this, Livepeer introduces the [Livepeer Media Server (LPMS)](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server) - an open source implementation of a media server which provides all of the live video specific functionality necessary for DApp developers and existing broadcasters to build live functionality into their applications. [Read more about it here](https://github.com/livepeer/wiki/wiki/Livepeer-Media-Server).

As a standalone application, any developer could build a live application on top of the LPMS, but it would still be centralized and would need to be scaled through traditional means. However when every node on the Livepeer network is running the LPMS, and the protocol’s economic incentives ensure that those nodes will contribute their processing power and bandwidth in service of transcoding and distributing live video, **a self-scaling, pay-as-you-go network is made available to developers, who can simply send their live stream into the network, and have the implementation details of scaling, payment, and media hosting abstracted away**.

## Livepeer Protocol ###########################################

The Livepeer Protocol defines how the various actors in a live streaming ecosystem participate in a secure and economically rational way. The two major areas that the protocol needs to address are the actual distribution of live video from the source to a large number of consumers in a performant and scalable way, and the economic incentives for encouraging participation in the network in a secure and game-theoretic manner. While this whitepaper will touch on the live video distribution itself where overlapping with the economic protocol, it will largely focus on the latter in order to demonstrate security and economic alignment. At the highest level, the protocol is designed to:

- Allow any node to send a live video into the network, and optionally pay to have it transcoded into various formats and bitrates.
- Allow any node to request the video from the network.
- Allow participants to contribute their processing power and bandwidth in service of transcoding and distribution of video, and to be compensated accordingly.

In a decentralized network where participants are rewarded in proportion to the amount of work that they contributed, the two big challenges that need to be addressed to ensure security are:

- Can it be verified that the work that the nodes did was done correctly?
- Are the nodes being awarded for real work that contributed value to the network, as opposed to fake work done in an attempt to gain rewards unfairly?

The Livepeer protocol is designed to address both the verification of work and the prevention of fake work, while also offering solutions for automatic scalability of the network and baked in governance for protocol evolution over time.

### Video Segments

The core unit of media within Livepeer is what we will call a `segment`. A segment is a time sliced chunk of multiplexed audio and video of time length `t`. Every segment in the Livepeer network is unique, and contains the cryptographic evidence to verify that the broadcaster intended this specific data for this specific segment. Each stream is made up of many consecutive segments, each containing a sequence number identifying their proper ordering. A segment contains the following fields:

| Video Segment Field | Description |
|--------|--------|
| **StreamID** | Identifies the origin node and stream that this segment belongs to. |
| **SequenceNumber** | The sequential order that this segment belongs in the original stream. |
| **DataPayload** | The binary metadata and data representing the audio/video in this segment. |
| **DataHash** | The hash of the data payload. |
| **BroadcasterSignature** | A signature from the broadcaster of `Priv(StreamID, SequenceNumber, DataHash)` which can be used to attest and verify that the broadcaster claims this to be the true data for this unique segment. |

The Livepeer protocol generally uses segments as the unit of work for transcoding, distribution, and payments.

### Livepeer Token

The Livepeer Token (LPT) is the protocol token of the Livepeer network. Broadcasters use LPT to broadcast video on the network and nodes requesting video pay LPT to retrieve the video. Nodes who contribute processing and bandwidth earn LPT in the form of fees from broadcasters and rewards from newly minted token. The token has the following uses in the network:

- It serves as the fuel for broadcasting live video through the Livepeer network.
- It serves as a bonding mechanism in a delegated proof of stake system, in which stake is delegated towards transcoders (or validators) who participate in the protocol to transcode video and validate work. The token, and potential slashing that occurs due to protocol violation, is necessary in order to secure the network against a number of attacks. More below.
- It is a unit of account that is specific to the Livepeer ecosystem, which forms the basis of a SectorCoin concept, applicable to additional functionality to be introduced in the future [[4](#references)]. Services such as DVR, closed captioning, ad insertion/monetization, and analytics can all plug into the Livepeer ecosystem using the Livepeer Token as a transfer of value.

An initial allocation of Livepeer Token will be distributed so that stakeholders can fulfill various roles in, and use the network, and then additional token will be issued on a predictable inflation schedule over time. See the [Token Distribution](#token-distribution) section.

Following the conventions of Ethereum and many popular ERC20 tokens [[16](#references)], LPT will be divisible by 10 ^ 18, with larger denominations such as the LPT itself intended to be used for user level transactions and staking, and smaller denominations intended to be used for protocol fees, microtransactions, and accounting.

### Protocol Roles

Before going forward, let’s define the roles in the network so that there is a common vocabulary for discussing the protocol. A Livepeer node is any computer running the Livepeer software.

| Node Role | Description |
|--------|-----------|
| **Broadcaster** | Livepeer node publishing the original stream |
| **Transcoder** | Livepeer node performing the job of transcoding the stream into another codec, bitrate, or packaging format. |
| **Relay Node** | Livepeer node participating in the distribution of live video and passing of protocol messages, but not necessarily performing any transcoding. |
| **Consumer** | Livepeer node requesting the stream, likely to view it or serve it through a gateway to their app or DApp’s users. |

In addition to the above roles played by users running Livepeer nodes, the protocol also will refer to the following systems. While we use certain specific systems to make reference to a possible implementation, alternative systems can also be swapped in if they provide similar functionality and cryptoeconomic guarantees:

| System Role | Description |
|-------|----------|
| **Swarm** | Content addressed storage platform. Data can be guaranteed to be available there temporarily during the verification process via SWEAR protocol [[7, 12](#references)]. *(Note in this document we refer to Swarm, but other content addressed storage platforms can be substituted if data availability can be guaranteed with high probability).* |
| **Livepeer Smart Contract** | Smart contract running on the Ethereum network [[1](#references)]. |
| **Truebit** | Blackbox verification protocol that guarantees correctness of computation placed on chain (at a hefty cost) [[6](#references)]. (<http://truebit.io>) |

Here is a visual overview of the roles, and the ways in which they communicate with one another in the work verification process described below.

<img src="https://s3.amazonaws.com/livepeerorg/LPProtocol.png" alt="Protocol Visual Overview" style="width: 750px">  

*Segments flowing from the broadcaster to the transcoder and eventually to the consumer. The transcoder ensures they have signatures and proof of work to participate in the work verification procedure.*

**Note on Transcoders:** Transcoders play the most critical role in the Livepeer ecosystem. They are the ones who are taking an input stream and converting it into many different formats in a timely manner for low latency distribution. As such they benefit from high availability, efficient, powerful hardware (potentially with GPU accelerated transcoding), high bandwidth connections, and solid DevOps practices. Transcoders should churn far less than other network participants, as when they take on the job of transcoding a stream, it’s less than ideal if they drop off the network. While the network can scale to support many participants playing the role of transcoder (and earning the requisite rewards), this is a special role that’s delegated towards from most network participants, in order to ensure that a reliable network that provides value to broadcasters is maintained. More below on this delegation.

### Consensus

Livepeer has a two layer consensus system. The LPT ledger and transactions are secured by the underlying blockchain, such as Ethereum. Any transfer of value of the LPT token or any transaction in the system can be considered to have been confirmed with the same security as the underlying proof of work or proof of stake blockchain. The second layer however, dictates the distribution of newly minted LPT. This is governed by the Livepeer Smart Contract, and participation in the protocol by various actors. While there is no consensus required per say, in terms of acceptance and validation of previous blocks, the protocol defines rules for participation and conditions upon which actors will be penalized (slashed) for failing to fulfill their role.

This second level of consensus governing the newly minted token is based upon Delegated Proof of Stake (DPOS), as inspired by systems like Bitshares, Steem, Tendermint, and Casper [[5, 9, 10, 11](#references)]. The role of validators in the network is played by Transcoders. Any user can delegate their stake towards a transcoder, who then needs to perform transcoding jobs in the network, participate in the work verification protocol, and invoke functions on chain at specific intervals to validate this work. The protocol will distribute token rewards and fees, and it will slash the stake of badly behaved actors. The validation result will be recorded on-chain via Truebit after it performs the validation, so there will be no room for disputes between the broadcaster and the transcoder.

### Bonding + Delegation

In Livepeer, in order to indicate stake in the network, nodes must bond some amount of their LPT. They do this through the `Bond()` transaction, which will tie up their stake in the smart contract until they `Unbond()`, at which point they will enter an unbonding state which will last for `UnbondingPeriod` time. Upon completion of the `UnbondingPeriod` they can then withdraw their LPT.

The bonded amount is used to delegate stake towards a Transcoder. The network supports `N` active transcoders at any one time, which is a moveable network parameter. Any node can indicate that it wishes to be a Transcoder with a `Transcoder()` transaction, and the protocol will select the `N` transcoders with the most cumulative stake (their own + delegated from other nodes) at the start of each round, along with one random transcoder from the waitlist.

Newly minted token in Livepeer is distributed to bonded nodes in relative proportion to the amount that they’ve bonded (minus fees), as long as they’ve delegated towards transcoding nodes that behave according to the protocol. Bonds can be slashed (reduced by a certain percentage) if the nodes that they’ve delegated towards do not behave and violate one of the slashing conditions. Nodes who have bonded and delegated towards a Transcoder also receive a portion of the fees that the Transcoder generates through transcoding jobs on the network.

Going forward, when this document uses the term "delegator", it is referring to bonded nodes who have delegated their stake towards a transcoder candidate, instead of delegating it towards themselves as a transcoder.

In summary, participants choose to bond their stake for the following reasons:

- Participate in delegating towards effective transcoders who will provide great service to the network, ensuring its value to broadcasters.
- Earn token rewards in proportion to stake.
- Earn fees generated from transcoders.
- They may wish to be a Transcoder.

### Transcoder() Transaction

A node indicates their willingness to be a transcoder by submitting a `Transcoder()` transaction, which publicizes the following three properties:

- `PricePerSegment`: the lowest price they are willing to accept to transcode a segment of video
- `BlockRewardCut`: The % of the block reward that bonded nodes will pay them for the service of transcoding. (Example 2%. If a bonded node were to receive 100 LPT in block reward, they would pay 2 LPT to the transcoder).
- `FeeShare`: The % of the fees from broadcasting jobs that the transcoder is willing to share with the bonded nodes who delegate towards it. (Example 25%. If a transcoder were to receive 100 LPT in fees, they would pay 25 LPT to the bonded nodes).

The Transcoder can update their availability and information up until `RateLockDeadline` time before the next transcoding round (Example 2 hours. They can change this information until 2 hours before the next transcoding round which lasts for `RoundLength` 1 day). This gives bonded nodes the chance to review the fee splits and token reward splits relative to other transcoders, as well as anticipated fees based upon the rate their charging and network demand, and move their delegated stake if they wish. At the start of a transcoding round, the active transcoders for that round are determined based upon the total stake delegated towards each transcoder, and stakes and rates are locked in for the duration of that round.

Here is an example state of Transcoder options that a delegator can review when deciding whom to delegate towards.

| Transcoder ID | PricePerSegment | BlockRewardCut | FeeShare |
|----|----|----|----|
| 1 | 22 μLPT | 1% | 25% |
| 2 | 30 μLPT | 2% | 40% |
| 3 | 10 μLPT | 4% | 1% |
| ... | ... | ... | ... |
| N | 14 μLPT | 0% | 2% |

*Note on price: In this document we list price/segment. In reality, Livepeer plans to use a gas accounting inspired model where there is a notion of units of gas required for certain job parameters of a segment such as bitrate, encoding, frame size, etc. Price/segment is a stand in, where the incentives are the same, but in reality they’ll likely be communicating price/gas.*

### Broadcast + Transcoding Job

Transcoders who are open for business on the network, throw their hat into the ring for transcoding work by submitting a `TranscodeAvailability()` transaction. This indicates their availability and places them into a pool of transcoders available to take a newly submitted job.

When a broadcaster submits their stream into the Livepeer network it is given a `StreamID`. This serves as both a unique identifier, and it also contains the origin node address so that nodes know how to request and route requests to consume this stream towards the origin. The stream contains many consecutive `Segments`, as described in the [Video Segments](#video-segments) section. If the broadcaster would like the network to take care of transcoding their stream into all the formats and bitrates necessary to reach every user on every device, then the first step is submitting a transcoding job transaction on chain. A job consists of:

`Job(StreamID, TranscodingOptions, PricePerSegment)`

The `TranscodingOptions` define the output bitrates, formats, encodings, etc, and the `PricePerSegment` lists the price that the broadcaster will offer.

As soon as this transaction is mined, the next blockhash will be used to pseudo-randomly determine the transcoder selected for this job. All transcoders with a price that’s lower than or equal to the price offered will be considered, and the blockhash modulus the number of candidate transcoders (weighted by their stakes) will determine the index of the selected transcoder.

At this point the broadcaster can begin streaming video segments towards the transcoder, and they’ll participate in the following protocol. The protocol also makes use of a persistent storage solution, for example Swarm, as part of the work verification process.

#### Preprocessing

1.  **Broadcaster**  -> **Livepeer Smart Contract**: submits a deposit on chain to cover the cost of the full transcoding job. This can be refilled later at any point, but the Transcoder may stop work if the deposit runs out as they gradually cash in for work done.

#### The Job

2. **Broadcaster** -> **Livepeer Smart Contract**: Job(streamID, options, price/segment)
    - Creates the job request on chain and places some token in escrow to pay for the work.
3. The protocol can use the next block hash to deterministically select the correct Transcoder for this job.
4. **Transcoder** -> **Broadcaster**: send output streamID and receipt that the job is accepted.
5. **Broadcaster** -> **Transcoder**: send stream segments, which contain signatures verifying the input data.
6. **Broadcaster** -> **Swarm**: Write input data payloads, using SWEAR params to ensure the data will be there long enough for verification (`PersistenceLength` time).
7. **Transcoder** performs transcoding and makes new output stream available on network
8. **Transcoder** checks **Swarm** periodically to ensure that the original stream data is there. If not, end the job at your discretion and claim your work.
9. **Transcoder**: Store a transcode claim for each segment of transcoding work. A transcode claim has the following fields.

| Transcode Claim Field | Description |
|-------|------------|
| **StreamID** | Identifies the origin node and stream that this segment belongs to. | 
| **Sequence Number** | The sequential order that this segment belongs in the original stream. |
| **Input Data hash** | The hash of the input segment data payload. |
| **Transcoded Data hash** | The hash of the output data after transcoding this segment. |
| **Broadcaster segment signature** | A signature from the broadcaster of Priv(StreamID, Seq#, Dhash) which can be used to attest and verify that the broadcaster claims this to be the true data for this unique segment. |
| **Transcoder segment signature** | A signature of all of the above fields from the transcoder attesting to the claim that this specific output transcoding was performed on this specific input. |

Whenever the transcoder observes that they are no longer receiving segments, they can call `EndJob()` to claim their work.

#### End Job

10. **Transcoder** -> **Livepeer Smart Contract**: Call `EndJob(StreamID, StartSegmentSeq#, EndSegmentSeq#, MerkleRoot)`. Transcoder is claiming on chain they have performed work on the claimed segment range, with a merkle root of all of the transcode claim data to commit to the content of these encoded segments.
11. Wait for this transaction to be mined, and observe the next blockhash. The protocol can then determine which segments will be verified based upon the `VerificationRate`.
12. **Transcoder** -> **Livepeer Smart Contract**: Provide transcode claims on chain for each segment that needs to be verified, along with merkle proofs for each segment in the transcode claims. The smart contract can verify the signatures from Broadcaster and **Transcoder** to ensure all data necessary is available to conduct verification, and can verify the merkle proofs against the committed merkle root from `EndJob()`.
13.  **Transcoder** -> **Truebit**: `Verify()`. This is an onchain call to the Truebit smart contract, where the Transcoder provides the Swarm input hash for the challenged segment. (More on verification in the following section)
14. **Truebit** -> **Livepeer Smart Contract**:  The result of the job is written on chain. This is compared to the transcoding claim result that the Transcoder provided.
15.  **Livepeer Smart Contract**: at this point the Livepeer smart contract has all the information it needs to determine if the Transcoder’s work is verified.
    - If verified correct, then use as input to token reward algorithm and release of escrowed fees.
    - If incorrect, then Transcoder and its stakers get slashed `FailedVerificationSlashAmount` and the Broadcaster is refunded.

The Broadcaster can stop sending segments at any point, which effectively is an `EndJob()`.

*Note: the above scheme has the Broadcaster writing all segments to Swarm. A cheaper alternative exists, which is the Transcoder just remembers the segments, and then only writes the challenged segments to Swarm, with Broadcaster signature as proof, before verification. Livepeer may use this scheme if there are no secondary benefits to having an archive of the stream on Swarm. Recording of the stream for future viewing is one such benefit however.*

At this point the transcoding has been performed, proof of the work has been claimed on the chain, and failure or success of the verification of the work has been reported. All the info is on chain to determine allocation of fees and newly minted token rewards to transcoders and delegators, or slashing in the case of failed verification. Let’s take a look at how work is actually verified.

### Verification of Work

In order to allocate token rewards and fees to transcoders who claim that they have performed a transcoding job, it’s necessary that the protocol can determine that the job was actually performed correctly with high probability. For this, Livepeer extends the research of, and makes use of, the [Truebit Protocol](http://truebit.io) [[6](#references)].

Truebit works by having one participant (the solver) perform the actual work for the fee, in this case transcoding, and then having additional participants (verifiers) verify the work in order to detect mistakes, errors, or cheating. The task is broken down into very small steps, and the verifiers check the work of the solver to find the first step that differs from what they expected it to be. Then, only this one very small step needs to be played out on chain by a smart contract (judge), who can tell which party did the work correctly. The economic incentives, including forced errors to incentivize checking on the part of verifiers, ensure that it is not profitable to cheat or challenge incorrectly, but it is profitable to play the role of checking the work.

The downside of this protocol is that it costs between 5x-50x the cost of the original work in order to verify all work. Livepeer uses Truebit as a black box to verify segments, but it gets around having to pay this very high verification tax by only verifying a small percentage of segments randomly, and using slashing in the case of failed verifications. The `VerificationRate` set within Livepeer determines how frequently a specific segment is to be selected for challenge within Truebit, and the randomness of a future block hash after the work has been committed to the blockchain, determines which segments specifically are selected.

If work is committed via an `EndJob()` call in block `N`, then

If `Sha3(N, BlockHash(N), Seg#) % VerificationRate == 0` then the segment # must be verified.

The Transcoder provides `TranscodeClaims()` on chain for the candidate segments. The Livepeer Smart Contract can verify the authenticity of these claims using the internal signatures and provided merkle proofs, and then invoke a call to Truebit to verify only these segments.

Truebit solvers and verifiers access the input data for a segment from a persistent content addressed storage system, such as Swarm. The Transcoder is responsible for verifying that the segment data is available in Swarm, and can optionally look for receipts from the SWEAR protocol [[5](#references)] guaranteeing persistence for a certain period of time, which is long enough for Truebit to play out. Additionally, they can take it upon themselves to run a Swarm node ensuring that the data is available to Truebit verification. If they have reason to believe that data is not available in Swarm, they can provide it, or just call `EndJob()` on the previously available data.

Truebit will write the results of the computation (succeeded or failed) back to the Livepeer Smart Contract, which can then be used in the reward and slashing calculations within the protocol. A transcoding node can not predict in advance which segments will be verified, and the following penalties will be felt in the case of cheating or failing to transcode correctly:

- `FailedVerificationSlashAmount` will be slashed if they fail a verification from Truebit.
- `MissedVerificationSlashAmount` will be slashed if they fail to provide transcode claims and invoke Truebit on segments they were required to do so.
- Lost fee from the broadcaster.
- Not only will the Transcoder be slashed, but all their delegators will be slashed as well. They will take this account into their decision of who to delegate towards, and the Transcoder could lose the lucrative job they hold.

It is important that it be more profitable to simply stake LPT towards a valid, honestly performing transcoder, than it can be to cheat and take slashing penalties while still collecting fees and rewards for dishonest work. Careful selection of the slashing params and verification rate can ensure this.

### Token Rewards

Livepeer is inflationary in that new tokens will be minted over time according to the schedule communicated below in [Token Distribution](#token-distribution). If all roles in Livepeer behave according to the protocol, then newly minted tokens will be rewarded to users in proportion to their bonded stake (minus fees). Transcoders have the role of taking turns calling the `Reward()` function in order to trigger the new token allocation or slashing which can be computed from all data available on chain.

Each transcoder will have a defined time window in which they are expected to invoke `Reward()`. This can be calculated by taking `RoundLength / (N * CyclesPerRound)` and shuffling the active transcoder order randomly each round using the starting block hash at the time of the round as the random input. When it is within a specific Transcoder's time window they must call `Reward()` which will execute the following steps.

- Ensure the correct Transcoder is calling `Reward()`
- Check to see if the previous Transcoders between the last `Reward()` call and this one took their turn and called `Reward()`. If not, slash them `MissedRewardSlashAmount`.
- Validate all eligible `TranscodeClaims` by this Transcoder since the last cycle.
- Validate the Transcoder’s `CompetitivenessTolerance` to ensure they were priced competitively to receive enough work. If not, there is no reward distributed.
- If there are any verification errors or missing verifications then slash by `FailedVerificationSlashAmount` or `MissedVerificationSlashedAmount`. Refund the Broadcaster.
- If there are no errors then mint the new token and distribute to Delegators and Transcoders relative to fee schedule.

Failure to invoke `Reward()` not only results in slashing, it also has the direct consequence of losing a portion of token rewards, and showing up as a ding on one’s Transcoder reputation when it comes to being elected by Delegators for the role.

### Slashing

As previously mentioned, the conditions for slashing are:

- Failing a verification
- Failing to invoke verification when required to do so
- Failing to call `Reward()`

One of the benefits of building within the Ethereum ecosystem are the network effect benefits you receive from being able to build on top of other protocols such as Truebit and Swarm/SWEAR. Unfortunately, with reliance on these external systems, which themselves have external dependencies and incentives, it’s possible that a flaw or weakness in one of those protocols could result in slashing within Livepeer.

For example, if a Truebit verification job sat in their queue for a long period of time without any solver or verifier claiming it, Livepeer would fail to see the result of that verification in time before `Reward()` was called. Or if the Swarm network suffered a partition and couldn’t propagate the file to the Truebit verifier in time, then this could also create an issue.

These risks can be mitigated by incentivizing these roles to be played in house by participants in the Livepeer protocol, who may find it in their best interest to serve as Truebit verifiers or Swarm nodes. But there’s also another approach which is introducing the concept of probability thresholds on the slashing parameters. Optional protocol variables such as `VerificationFailureThreshold` could be set to indicate that as long as the node passes 99% of verifications they won’t be slashed for example. This will remain a further area of research to be worked our prior to network deployment.

Slashed funds will enter the `CommonPool`, which can be burned or allocated to common uses such as further ecosystem development, according to the governance mechanism of the protocol.

### Token Distribution

As a token that represents fuel for broadcasting video within the Livepeer network, the Livepeer Token distribution will follow a similar model to other fuel based token distributions such as Ethereum.

An initial allocation of the token will be distributed to people purchasing it to broadcast within the network or to stake into the role of Transcoder or Delegator. The proceeds of the distribution will be used in order to fund the future development of the protocol and bring it to market. A portion will be allocated to groups who contributed prior work and money towards the protocol before the sale, and a portion will be endowed to a Foundation in order to support ongoing development over time.

At the launch of the network, token issuance will continue according to an inflationary schedule of a fixed `TokenInflationRate`% per year of the original issuance amount. Over time this inflation trends towards 0% of the total supply. However there will still be additional LPT entering the market as an incentive to Transcoders/Delegators and to replace lost LPT.

<img src="https://s3.amazonaws.com/livepeerorg/LPTInflation.png" alt="Sample Token Inflation" style="width: 640px">

*Sample inflation of token supply vs total float over the first 100 years if the `TokenInflationRate` is set at 26%*

### Governance

The role of governance within the Livepeer protocol is intended to be two fold:

1. Determine the burning or appropriation of common funds which were slashed from misbehaving nodes.
2. Adjust network parameters to ensure a healthy, thriving network which is valuable to broadcasters.

Many of the network parameters referenced in this document such as `UnbondingPeriod`, `RoundLength`, and `VerificationRate` are adjustable. Proposals for adjustments to these parameters can be submitted, and the governance process, including voting by transcoders in proportion to their delegated stake, will determine adoption of these changes automatically within the protocol. The detailed spec for governance is left for another document. [See more here](https://github.com/livepeer/wiki/wiki/Governance). 

## Attacks

This section contains a survey of the various ways that malicious actors may try to attack the Livepeer network. We use a rational attacker model in which the attacker makes decisions based upon their own economic self interest. A number of attacks are mitigated via it being unprofitable to conduct such attacks, but we also strive to ensure that at the worst the network suffers decrease of efficiency in the case of a sustained unprofitable attack, and doesn't suffer a failure.

### Consensus Attacks

As mentioned previously, consensus in the Livepeer ecosystem is provided by the underlying blockchain platform (Ethereum for example). 51% attacks, double spends of Livepeer Token, and forks of the network would require the same resources and cost-of-attack as Ethereum itself.

Livepeer is a staked based protocol, and while Transcoders have the role of participating in the work verification process and the token reward distribution process, they actually do not have the role of validating or accepting other Transcoders' work. There is no concept of a chain, nor is there validation of previous blocks. There simply exists the economic incentives to verify one's own work and distribute one's own portion of token rewards when it is one's turn. As such, attacks that are seen in a proof of stake protocols such as the Long Range Attack, the Nothing at Stake problem, and The Bribe Attack don't apply, as there is no opportunity to attempt to sign multiple blocks or attempt to create a longer chain from an earlier state. However, one should be aware that as the underlying blockchain migrates to proof of stake, these attacks do threaten to undermine Livepeer if the benefit of carrying them out on Livepeer were to exceed the cost of attack on Ethereum itself.

While relying on the security of the underlying blockchain is nice for prevention of consensus attacks, there still exists a class of quality and efficiency attacks that can harm the Livepeer network.

### DDoS

Denial of Service in Livepeer can go two ways:

1. A Transcoder can try to prevent or slow down a Broadcaster from getting their encoded stream out to the network by accepting a job but refusing to transcode.
2. A Broadcaster can prevent a Transcoder from being able to do the job that they believe they were assigned by refusing to send them segments.

Both attacks have a cost and can be mitigated, with slight annoyance.

In the first case, a Transcoder has to pay to claim their availability on chain. If they are not going to receive a fee because they didn't do the work, then they're throwing token away. The Broadcaster can just resubmit the job and be assigned a new Transcoder. One potential option for scalability is that the protocol can identify a number of valid Transcoders in priority order instead of just one, and this way the Broadcaster can just move on without another on chain transaction. Additionally, all stats about accepted jobs and average # of segments transcoded/job, etc, can be calculated from on-chain data, and delegators would use this as input into their decision about whom to delegate towards. Behave poorly and lose your role.

In the case of a Broadcaster preventing a Transcoder from doing work, this is merely a capacity planning calculation. A Transcoding node can maintain records of its capacity for concurrent jobs, likelihood of a job being active/inactive, and ensure that it always believes it will have capacity for the work that it claims. Simply ignoring or calling `EndJob()` on a node that's refusing to send segments hardly hurts the Transcoder.

### Forced Slashing

If a Broadcaster were to try to attempt to get a Transcoder slashed, they would probably do so by not writing all their segments to Swarm or not paying for SWEAR receipts to guarantee their availability for Truebit verification. It is on the Transcoder to check Swarm to ensure the data is there before claiming segments, but doing this for every segment may potentially be costly depending on Swarm pricing dynamics. Since this is unknown at the moment, the various solutions are on the table:

1. Transcoder writes the data to Swarm itself, using the signature from the Broadcaster as proof of the correctness of the content.
2. Transcoder requires SWEAR receipts from Broadcaster.
3. Transcoder uses probability calculations to determine how often to check segments, and align this with the `VerificationFailureThreshold` to ensure that they remain above the threshold despite risking failing a couple verifications.

### Useless or Self Dealing Transcoder

If a Transcoder has enough stake to maintain their position, they could theoretically list a 100% `BlockRewardCut`, 0% `FeeShare`, and charge a high `PricePerSegment` such that they would never have to do any work, yet could collect their token rewards. This is prevented by the `CompetitivenessTolerance` which requires them to contribute some amount of valid work. Additionally, because of the transaction costs of participating in the protocol incurred by Transcoders, it would be more profitable for them to simply stake their token toward a valid Transcoder who was sharing fees with them, than it would be to act as a useless Transcoder who would receive no fees to speak of.

A misbehaving Transcoder who is outputting invalid output would quickly get slashed down to the point of their stake being reduced too low to actually keep their job and receive any work.

### Chain Reorg

When a broadcaster submits a job to the Livepeer Smart Contract, the protocol uses the current block hash to determine which transcoder will be assigned the job. Reorganizations of the underlying blockchain can cause confusion in this scenario. While this is not "an attack" directly, a transcoder will be valid one second, and then upon reorganization, will no longer be valid. When a reorg is detected the broadcaster can either redirect the stream towards the new valid transcoder, or the protocol can detect uncle blocks that are included in the main chain, and consider a transcoder to be valid if an uncle block within a given threshold would have made them valid. 

## Live Video Distribution ###########################################

This whitepaper has largely focused on the economic incentives and protocol for ensuring proper transcoding of live video, which is necessary to support adaptive bitrate streaming and reach every device. But equally important is the distribution of video throughout the network so that it can be consumed with high quality and low latency. The economics of distribution rely on tit-for-tat bandwidth accounting as popularized by Bittorrent, and extended via protocols like SWAP [[13](#references)]. As a simplification, nodes pay to request a segment of video, and nodes get paid to serve a segment of video. If a node already has a segment and can serve it to multiple requestors, it is profitable. We call this type of node, a Relay node.

Different incentives exist when it comes to bandwidth for nodes playing different roles in the network.

* Consumers may be willing to exchange upstream bandwidth to serve the content to additional Consumers in exchange for being able to consume the video themselves free of charge. See systems like Webtorrent [[14](#references)].
* Broadcasters serve as origin nodes and may want to charge for consumption of the video, or may want to subsidize the cost of bandwidth so that everyone can access their video for free.
* Transcoders and Relay nodes are willing to provide bandwidth in service of distributing video as long as it is profitable. This is similar to the role of traditional CDNs.

With `Segments` as the core unit of data flowing through the network, it is possible to do tit-for-tat bandwidth accounting using the Livepeer Token as the basis for settlement. We borrow the Chequebook Contract abstraction from Swarm [[6](#references)] as a method of offchain payment passing with on chain settlement. Future developments in the ecosystem including the Raiden Network [[15](#references)] may allow of payment channels to be used for this purpose as well. Since token transfer is native to the protocol, it is also possible to embed pricing associated with content directly into the protocol. A broadcaster can charge for their time or content directly, and nodes will opt into this transfer of value by paying a higher price/segment which will flow back to the broadcaster.

What's important to note is that while bandwidth accounting can be used to make it profitable to run Relay Nodes which just pass video segments around the network to add capacity, a-la a CDN, these nodes are purely incentivized by demand for the content, and not incentivized by newly minted token rewards. In fact, the output of Livepeer can be inserted into a traditional CDN (like Amazon S3, Cloudflare, etc) or decentralized CDN (like IPFS or Swarm). Development of this peer-to-peer protocol for video segment distribution itself will be an ongoing opportunity for optimization and improvement in performance. As non-critical to the cryptoeconomics of the Livepeer protocol, the details are spared from this document, but the interested can [follow along here](https://github.com/livepeer/go-livepeer) with the development, and look for a future document addressing purely the video distribution protocol.

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

In summary, the Livepeer protocol incentivizes nodes to contribute their processing and bandwidth to the network in service of transcoding and distributing live video. The verification of work is solved by a scalable extension on top of the Truebit protocol which incentivizes nodes to perform transcoding operations correctly in order to earn their fees and block rewards and preserve their value earning role as a transcoder. The gamification of the network and false work problem is solved via the economics of the delegated proof of stake block reward distribution. It becomes more economically rational to simply stake one's tokens towards a value adding node than to pay fees into the network to be distributed to other delegators when performing work that there wasn't actually real demand for.

The end result is a scalable, pay-as-you-go network for decentralized live video broadcast - a missing layer in the web3 stack that Livepeer seeks to fill.

## Appendix ###########################################

### Livepeer Protocol Parameter Reference

| Parameter Name | Description | Example Value |
|----|------|---|
| `T` | Segment length in seconds | 2 seconds |
| `N` | Number of active transcoders | 144 |
| `RoundLength` | Length of time between election of a new round of transcoders | 1 day |
| `CyclesPerRound` | Number of times each Transcoder is expected to call Reward() during a Round. | 2 |
| `RateLockDeadline` | Transcoders rates lock in this amount of time prior to the next round start time so that delegators can review and delegate accordingly. | 6 hours |
| `UnbondingPeriod` | Time between entering unbonding state, and ability to withdraw the funds. | 1 month |
| `PersistenceLength` | The minimum period that a receipt of data persistence must be provided in the decentralized storage solution. | 6 hours |
| `VerificationRate` | The % of segments that will be verified. | 1/500 |
| `FailedVerificationSlashAmount` | % to slash in the case of a failed verification (beyond the potential allowed failure threshold) | 5% |
| `MissedRewardSlashAmount` | % to slash in the case of missing a block reward round (Maybe only do this in the case of n consecutive misses) | 3% |
| `MissedVerificationSlashAmount` | % to slash in the case the transcoder didn’t call verification | 10% |
| `CompetitivenessTolerance` | If all transcoders were always available and set the same price and fees, they would receive work in proportion to their stake. This parameter sets a % that they have to be within this target work % to be eligible for rewards. This prevents transcoders from doing very little share of work relative to their stake. | 90% (extreme example. With 100 transcoders and 100,000 segments, this means I am ok if I only did 100 segments (10% of the 1000 I was supposed to do)). |
| `*SlashingThresholds` (TBD) | Placeholder to indicate that we may not slash on all failures, only if they exceed some threshold % of failure rate. | |
| `VerificationFailureThreshold` | % of verifications you can fail without being slashed. Useful because of external dependencies like Swarm/Truebit that could cause sporadic failure. | 1% |

### Livepeer Protocol Transaction Types

| Transaction | Description |
|----|------|
| `Bond()` | Bond stake towards a transcoder. |
| `Unbond()` | Enter the unbonding state for the fixed `UnbondingPeriod`. |
| `Transcoder()` | Declare your intentions as a transcoder. |
| `TranscodeAvailability()` | This transcoder is currently open to accepting another job. They’re in the pool to be assigned randomly on new job submissions. |
| `Job()` | Submit a transcoding job on chain. |
| `Deposit()` | Submit a deposit on chain that will be used and drawn against to pay for jobs. |
| `Withdraw()` | Withdraw from deposit and unbonded stake. |
| `EndJob()` | End the transcode job and make the claim of which segments you can prove you’ve transcoded. |
| `TranscodeClaims()` | Transcoder provides the transcode claims for segments which will be verified along with merkle proofs for comparison with merkle root from `EndJob()`. Invokes Truebit using data in transcode claims. |
| `Reward()` | Does all the verifications on chain to either slash or distribute token rewards. Can only be invoked by a transcoder when it’s their turn and time window. |
| `Verify()` | An explicit call to Truebit. May be unnecessary if this is included in `TranscodeClaims()` transaction. |
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
