# Livepeer Streamflow Paper

**Livepeer Scalability on Ethereum through Orchestration, Probabilistic Micropayments, and Offchain Job Negotiation**

**Authors**    
Doug Petkanics <doug@livepeer.org>    
Yondon Fu <yondon@livepeer.org>

**Researchers**    
Eric Tang <eric@livepeer.org>    
Philipp Angele <philipp@livepeer.org>    
Josh Allmann <josh@livepeer.org>

**STATUS: PROPOSAL - Feedback and review is requested on this early proposal.**

## Abstract #####################################

The Streamflow proposal introduces updates to the Livepeer protocol and offchain implementations which will allow Livepeer to scale beyond the current limitations of the alpha protocol deployed to the Ethereum blockchain. It suggests updates that address affordability, reliability, performance, and scalability of the network. Key elements are introduced including a service registry, an offchain job negotiation and payments mechanism, a split between orchestration nodes and transcoding nodes, the elimination of the data availability problem solution as a dependency on trustless verification, and the opening up of the number of nodes that can compete to perform work on the network from the low arbitrary limits during the alpha. The resulting architecture will allow users of the network to perform high scale transcoding jobs on the network across many concurrent work providers, while significantly reducing the impact of the underlying blockchain's demand and price volatility on the economic viability of using the network. 

## Table of Contents ###########################################

* [Introduction and Background](#introduction-and-background)
* [Streamflow Protocol Proposal](#streamflow-protocol-proposal)
    * [Orchestrators and Transcoders](#orchestrators-and-transcoders)
    * [Relaxation of Transcoder Limit and Stake Enforced Security](#relaxation-of-transcoder-limit-and-stake-enforced-security)
    * [Service Registry](#service-registry)
    * [Offchain Job Negotiation](#offchain-job-negotiation)
    * [Probabilistic Micropayments](#probabilistic-micropayments)
    * [Fault-based On Chain Verification](#fault-based-on-chain-verification)
* [Economic Analysis](#economic-analysis)
    * [Livepeer Token](#livepeer-token)
    * [Delegation as Security and Reputation Signal](#delegation-as-security-and-reputational-signal)
    * [Inflation Into Bonded State and Apathetic Delegators](#infaltion-into-bonded-state-and-apathetic-delegators)
    * [Offchain Engineering Considerations](#offchain-engineering-considerations)
* [Attacks](#attacks)
    * [Delegator Squeezing](#delegator-squeezing)
    * [Delegator Fee Theft](#delegator-fee-theft)
* [Open Research Areas](#open-research-areas)
    * [Non Deterministic Verification](#non-deterministic-verification)
    * [Public Transcoder Pool Protocols](#public-transcoder-pool-protocols)
    * [Broadcaster Doublespend Mitigation](#broadcaster-doublespend-mitigation)
    * [VOD Payments](#vod-payments)
* [Migration Path](#migration-path)
* [Appendix](#appendix)
    * [Appendix A: Probabilistic Micropayments Workflow](#appendix-a-probabilistic-micropayments-workflow)
* [References](#references)

## Introduction and Background ###########################################

The Livepeer protocol incentivizes and secures a decentralized network of video transcoding nodes. Users who would like to transcode video can submit a job to the network at a price they determine to be acceptable, be assigned a transcoder, have the video transcoding performed with economically secured guarantees of accuracy. The live protocol uses a delegated-stake based mechanism for electing the nodes who are deemed reliable and high quality enough to perform live video encoding in a timely and performant manner. 

The alpha version of the protocol currently deployed on the Ethereum blockchain has implemented many of the designs originally specified in the [Livepeer Whitepaper](https://github.com/livepeer/wiki/blob/master/WHITEPAPER.md). The delegated stake based system, with its inflationary incentives, has shown to be effective in incentivizing participation, and creating an engaged early network of transcoders and delegators to perform transcoding work and QA accordingly. The network is usable, and for a number of use cases such as long running live transcoding, or decentralized app prototyping, is a viable option today in its early state. However, for the scaled usage of video infrastructure services, the alpha version suffers from the following weaknesses:

1. Cost of using the network is too correlated to fluctuations in Ethereum gas pricing, and therefore at times of high gas prices, or encoding scenarios which require many transactions, the network becomes too expensive to be viable relative to centralized alternatives.
1. Stake based job assignment and on-chain transcoder negotiation creates unreliable scenarios for the broadcaster - if their assigned transcoder goes offline, they incur additional costs and delays in negotiating for a second transcoder, which can be prohibitively disruptive in a live streaming context.
1. The data availability problem remains unsolved (in production), and therefore verification of work can not be fully trustless and non-interactive. 
1. Transcoders have no way of managing their availability to perform or not perform jobs depending upon capacity and workload beyond stake.
1. While the network encourages price competition, it does not encourage performance competition and accountability directly.
1. Current limitations of Ethereum gas limits and the protocol’s practical implementation restrict the number of active transcoders who can be active at any one time to a very low number, creating a high barrier to entry to compete for work on the network.

The rest of this paper proposes solutions that address each of these weaknesses in turn. It leads off with a description of the architectural and protocol change proposals. It then analyzes the economic impacts of these changes on the network, before addressing the possible attacks. It moves on to acknowledge the open research areas which can contribute to taking this proposal from economic and social/reputation based security to strongly, cryptographically assured security. And it will finally conclude with some thoughts on a migration path from the alpha protocol to Streamflow in the live network, should the community wish to accept these changes.

This paper describes the conceptual protocol changes and analyzes their impact, but leaves the specific specifications for their implementation to an accompanying SPEC document to be provided as part of the Livepeer Improvement Proposal (LIP) process.

_Note: To properly absorb the protocol updates, it's important to have an understanding of how the current Livepeer protocol works, as described in the Whitepaper [[1](#references)]._


## Streamflow Protocol Proposal #################################

This proposal introduces a number of changes and new concepts into the Livepeer ecosystem. Each delivers impacts across one or many of the areas of affordability, performance, reliability, or scalability. They include:

* Introduction of a new role of Orchestrator, to the existing roles of Broadcasters and Transcoders. 
* Relaxation on the limitation on number of transcoders, allowing open access to compete for work amongst any aspiring token holding Orchestrator meeting the minimum stake and security requirements.
* Service registry in which Orchestrators advertise their available capabilities and services on chain.
* Offchain price negotiation and job assignment between Broadcasters and Orchestrators.
* Offchain payments using Probabilistic Micropayments, with on chain settlement and security deposits.
* Updated verification flow, in which on chain verification only needs to occur in the case of an observed fault.

### Orchestrators and Transcoders

Currently a Transcoder on the Livepeer network is a protocol-aware node that both watches and interacts with the blockchain protocol and performs video transcoding work. In short, it both orchestrates work on the network, and transcodes video. This can create performance and reliability issues, and make it difficult for nodes to scale their operations. Streamflow proposes a two tiered architecture, which contains a split between:

* An Orchestrator which is protocol aware, negotiates work with Broadcasters, is responsible for delivering verified transcoded segments to the Broadcasters, and coordinates the execution of this work amongst a potentially large pool of transcoders.
* A Transcoder which is not necessarily aware of the Livepeer staking protocol or blockchain, and instead is just competitive, cost-effective hardware, which does the sole job of racing to transcode video as cheaply and quickly as possible, as coordinated by Orchestrators.

<img src="https://livepeer-dev.s3.amazonaws.com/docs/otsplit.jpg" alt="Orchestrator Transcoder Split" style="width:750px">

Tier one of this architecture is similar to the current Livepeer protocol, wherein current Transcoders are renamed to Orchestrators. These Orchestrators stake LPT to provide security deposits against the work that they perform, such that should they harm the network, they incur economic penalty. Broadcasters are aware of these Orchestrators, negotiate jobs with them, and receive transcoded segments back from them, with the ability to slash the Orchestrators if they perform work dishonestly. 

Tier two of this architecture is a new concept called the Transcoder Pool. The job of actually racing to perform video transcoding as quickly and cheaply as possible can be performed by GPUs who have available capacity, such as those described in the Video Miner Proposal[[2](#references)], which have NVENC asics sitting idle. This hardware should just be able to compete to perform the actual encoding work, and the competitive forces and economics should result in significantly cheaper prices than if Orchestrators themselves needed to perform the work on the same CPU based setups required to run blockchain and protocol aware orchestration on the network.

This is analogous to current cryptocurrency mining pools, in which the implementation for the pools themselves can be centralized or decentralized; they can be run by the central pool operator themselves or they can be open to tap into the millions of available computers around the world all competing to mine the next block. An Orchestrator can provide the transcoding for their own pool if they’d like, which results in the same setup as exists in the Livepeer protocol today, though by doing so they have to be expert at two very separate and distinct jobs, and they compete with those Orchestrators who open up their pools to potentially faster or cheaper sources of transcoding. On the other hand, if an orchestrator runs their own pool then they can trust the video encodings without having the verify the work of the public untrusted transcoders.

Some benefits of this two-tiered setup include:

* Anyone who wants to earn fees can do so simply by turning on their hardware and have it race to perform available transcoding jobs. No on chain knowledge, cryptocurrency, staking, deposits, need necessarily be required. This is much like the way anyone can earn bitcoin by mining as part of a pool. However, while access is open to everyone to compete, transcoders with advantages in electricity, bandwidth, and geolocation will likely outperform those running without these competitive setups.
* Broadcasters get the pooled security of an Orchestrator’s on chain stake, but the underlying scaling implementations and security of public and private pools can be left up to experimentation outside the scope of the Livepeer protocol.
* Orchestrators can focus on proper operations for protocol interactions and security, rather than focusing on scaling hardware. One orchestrator could orchestrate hundreds of streams concurrently without having to transcode the video itself.
* Alternate transcoder pool implementations can be available, leveraging GPUs, distributed setups to race for jobs in different regions, and encouraging competition which will result in cheapest available prices for Broadcasters.

While this paper will describe the protocol for Broadcaster/Orchestrator communication and security, it leaves the second tier of Orchestrator/Transcoder protocol to varying implementations. In the simple case where an Orchestrator is its own Transcoder Pool, then the protocol’s security holds, while other trust/performance tradeoffs can be made in alternative implementations for coordinating pools, ranging from centralized and trusted, to decentralized secured by blockchain based stakes and deposit within layer two. It is theorized that since verification of work performed by random actors in a public pool incurs additional cost to an O, then private pools may outperform public pools, however this can potentially be overcome by solid cryptoeconomic deposits, slashing, and verification protocols. 


### Relaxation of Transcoder Limit and Stake Enforced Security

The second major change proposed by Streamflow is to relax the artificial limit on number of active Transcoders (Orchestrators in Streamflow). At genesis this parameter was set to 10, and has since expanded to 15, however this still creates a major barrier to entry, in that a node needs increasingly more LPT staked in order to enter the active pool. In Streamflow, the goal is to remove this arbitrary limit and to allow any node who provides enough security in the form of stake (or delegated stake) access to compete on the network.

The reasons for the limit in the first place were:

* With work assigned on chain to active transcoders, it was critical that they be online and available to perform the work. A constraint on the availability of this position, and easy visibility of their statistics and performance helped ensure a high quality network.
* Ethereum gas limitations on the calculation and bookkeeping around this active set, created an artificial limit, which could still expand beyond the current point but not by an order of magnitude.
* During the alpha it was important to be in close contact and coordination with the active set so that they could update software frequently, respond to bugs, and help develop and QA the network.
* Active transcoders needed enough stake at risk to secure the network, such that if they cheated they would receive a steep economic penalty.

The effects on the above of the reduction in this artificial limit will be realized by:

* Offchain job negotiation and failover, meaning that Orchestrators who aren’t available or don’t perform work will just lose future work, but won’t hurt the Broadcaster experience.
* The active set won’t have to be calculated per round, and instead can just be maintained in place as Orchestrators bond, unbond, or get slashed.
* Active, competitive Orchestrators will still want to pay close attention to upgrades, bugs, and the development of the network - but inactive Orchestrators who don’t will simply fail to attract work on the network without hurting the Broadcaster experience.
* Now that a large percentage of the initial stake is actively participating, the requirements can be set such that enough stake and security is in play to secure a larger number of nodes competing for work on the network.

The exact number of target Orchestrators and implementation method is still an open research problem. Initially, there should be an order of magnitude increase - such as hundreds of active Orchestrators rather than 15 - with a goal of eventually expanding into the 1000's in order to offer each service in every region in the world with redundancies. Here are a few considered mechanisms, with a short description of some of their tradeoffs:

1. **Expand N (# of orchestrator slots) from 15 to something much larger, such as 200**: Things would essentially work the way they do today, with a much lower barrier to entry to activating a node. But this would make bonding related actions more expensive. Ethereum scaling and gas issues may come into play.
2. **Set a minimum required stake to become an Orchestrator**: This would establish a maximum possible `N`, while allowing anyone to know exactly what it takes to achieve that security bar and remain in the active set. It would also enable an expanding network of Orchestrators as inflationary LPT is generated, and encourage Delegators to actively seek out new potential Orchestrators offering fee shares, who are looking to surpass the minimum to become active to compete for work.
3. **Set a fixed stake amount for any Orchestrator**: This would force Orchestrators to run additional nodes, and Delegators to constantly restake, in order to put their inflatiory LPT to use. But it comes with some weaknesses around the resulting user experience for both Orchestrators and Delegators, as well as some complex implementation details.
4. **Eliminate any minimum stake requirement from the protocol, and let clients configure how much stake is required to secure a job**: This creates the most open access and is the most decentralized initially, however it offers the least coordination between token holding Delegators and Orchestrators aligning to create a high quality network - essentially reputation plays a larger role, and therefore it could lead to more centralization of the work performed over the longer term as delegators have less collective ability to route work.

While the benefits and weaknesses of the above approaches are being considered, it's important to note that the result achieved from implementing any of the above will be an expanded Orchestrator network, more redundancies and competition provided to the benefit of Broadcasters, and the continued incentives to route stake towards nodes who can perform additional services reliably and cost effectively to the network in exchange for fees.

One of the benefits of the minimum stake models is that as fees flow through the network, there is little reason to operate a node that isn't competing for work on the network. The number of slots is limited, and that stake is better put to use delegating towards a node that would provide a fee share, than simply sitting on an idle node only collecting rewards. 


### Service Registry

Streamflow expands the role of the Service Registry in the on chain protocol. Orchestrators will continue to advertise their `rewardCut`, `feeShare`, and connection information, however they will also advertise the services that their node is offering, and region(s) their node is serving. This will lead to performance impacts and Broadcasters can look for the specific services they want, served by a nearby node. Orchestrators will no longer advertise the price that they are charging, as price and availability negotiation is moving off chain. As for considered services, there are likely two abstractions:

1. **Service**
    1. Service identifier - the id that represents this particular service, such as “CPUTranscoding”, “GPUTranscoding”, or “SegmentVerification". There is still work to be done on the exact definition here, and it’s possible the services are more granular such as input/output encoding pairs such as “H264 1080p -> 720p”. 
    1. Verification function - the address pointer to the verification function which will be run to invoke on chain verification of the correctness of this service (can be null if there is no verification available). 
1. **Locations**
    1. Implementation is TBD, but this is likely an abstraction that specifies an array of the regions that this node is willing or able to serve. 

The combination of advertising these will allow Broadcasters to filter the Service Registry for nodes whom are advertising the services and locations that they would like to serve in order to be efficient in beginning an offchain negotiation with the proper service providers. Location was a previously ignored factor in the alpha version of Livepeer, however it can be critical for live video ingest that the nodes receiving the video are located in close proximity to the video source, due to various networking issues that can occur and create instability over longer connections with more hops.

An advertised location can of course be falsified, however, like many aspects of Streamflow, client implementations will quickly discover and filter out poorly performing Orchestrators costing them the ability to do future work and earn fees. Honestly performing nodes, maximizing their client-calculated Broadcaster success relationships, fees, and reputational statistics will likely advertise helpful location information that leads to successfully negotiated, assigned, and sustainable long running jobs.

As nodes earn inflationary LPT, in order to put it to optimized use, the most effective thing they can do is add a new node to the service registry which serves a capability or location for which there is demand, but not enough reliable or cost effective supply - therefore expanding the footprint of the network and ability to serve various customers and use cases. 

### Offchain Job Negotiation
The shift from on chain job assignment to off chain job negotiation is perhaps the biggest change proposed by Streamflow. It changes the assumption that jobs are routed strictly according to stake, and this will be analyzed below in the analysis section, but it also comes with tremendous benefits. Namely:

* **Availability** - Broadcasters will be able to ensure that Orchestrators are available to do work before contracting with them.
* **Redundancy** - If an Orchestrator is unavailable before or during the job, simply switch to another Orchestrator. Or begin working with multiple orchestrators in the first place for redundancy.
* **Speed** - Begin work immediately. There is no need to wait for an on chain confirmation.
* **Cost effective** - There is no on chain job or gas costs associated with requesting service on the network.

In order to conduct a negotiation, a Broadcaster will interact with the following protocol:

1. Read the Service Registry and scan through all available Orchestrators that match their requested service and location parameters, with the minimum required stake.
1. They will then use the provided connectivity information to ping each of them with a job request.
    1. A job request contains the service requested and location requested (optional).
1. Orchestrators respond as quickly as possible with a price quote for performing the job, if they would like to compete for it and have current availability.
    1. Orchestrators also include probabilistic micropayments (PM) parameters in their price quote (described below).
1. Broadcasters collect this response data, along with the response times from the orchestrators.
1. They run their own internal algorithm taking into account preferences with regards to response time, price, past work history, PM params, redundancy requirements, security in the form of stake, in order to elect which Orchestrator(s) to work with.
1. They begin sending video segments and PM tickets to the selected Orchestrator(s).
1. Orchestrator verifies Broadcaster’s on chain PM deposit, and if the deposit level is sufficient, it performs work sending encoded segment back to Broadcaster.

Clearly step 5 in this protocol leaves a lot up to implementation. The summary here is that Broadcasters can choose their own Orchestrators, and they don’t need to go on chain to announce the job or be assigned one. 

They can work with their own Orchestrator if they’d like, and then start sending segments only to another candidate when they reach their own compute capacity. They can work with the same node that they have a long standing relationship with, and only switch over to another when that node goes down or becomes unavailable. They can start with 5x redundancy CPU encoding from the beginning for a very important premium live stream, or they can use the cheapest possible GPU encoding across the world for a very low reliability on demand job in order to save costs.

<img src="https://livepeer-dev.s3.amazonaws.com/docs/pricenegotiation.jpg" alt="Offchain Job Negotiation" style="width: 750px">

Switching and adding redundancy does not introduce any on chain transaction cost overhead for the Broadcaster, whereas in the alpha version of the protocol, switching requires an additional on chain transaction and 15-30+ second confirmation times.

Note that steps 1-4 can optionally be performed in the background on an ongoing basis, rather than at stream inception. If a Broadcaster is handling many concurrent streams, they may find it worth it to keep an up to date price/service table for all available Orchestrators, such that they can just begin working with one at any moment on any stream.


### Probabilistic Micropayments
The largest impact on cost savings from Streamflow will come from this Probabilistic Micropayments (PM) proposal. Formerly, the protocol used a deposit() -> job() -> claim() -> verify() -> distributeFees() transaction flow to release payments for performed work. The last three of these transactions needed to be performed for every 1000 segments of video on average (or more), and doing five transactions for a short job would be completely cost prohibitive for Transcoders.

For background on PM, it is suggested to review a post from the Orchid Protocol team on its use in a decentralized VPN network, as well as the previous academic research[[3, 4, 5](#references)]. The summary is that the Broadcaster issues signed tickets along with every single segment of work to the Orchestrator. The ticket has a high face value if it “wins”, allowing the Orchestrator to cash it in on chain for that high amount. However, the probability of it winning is very low, so the expected value of each ticket is the price/segment that the Broadcaster and Orchestrator agree upon. Over the long term, Broadcasters will pay nearly exactly what they agree per segment to Orchestrators, and Orchestrators will be paid nearly exactly the correct amount for the work they performed, due to the probabilities at work.

By using PM, the cost of collecting payments can be the cost of a single lightweight transaction, and the payment amount collected can effectively be batched into whatever amount the Orchestrator is willing to cash. For example, the Orchestrator can always cash in payments of $10 worth of ETH, whereas the cost of cashing the ticket due to gas prices may be $0.10, for a 1% overhead. If gas prices increase 10x, the Orchestrator can instead cash payments of $100, maintaining the same 1% overhead, or they can absorb more overhead if they’d like to do so to be competitive. Fitting with philosophy driving many of the Streamflow proposal updates, it will be market driven and client configurable rather than protocol enforced. 

A Broadcaster’s ability to pay when an Orchestrator cashes out is secured by an on chain, time locked deposit, and penalty escrow. 

Due to the offchain job negotiation and potential redundancies a Broadcaster may require, they can send PM tickets around to many orchestrators at once, start and stop work with any one Orchestrator at any time, and likewise, and Orchestrator can stop performing work at any time for a Broadcaster if they determine they aren’t paying correctly or want to go offline. This shifts the mental model tremendously from a “Job” in Livepeer being an entire continuous stream, to a Job being a single segment of video along with a single PM ticket.

The full PM workflow is left for [an appendix](#appendix), since it touches on verification, off chain negotiation and many other areas such as double spend risk and mitigation.

### Fault-based On Chain Verification
The final major change proposed by Streamflow is to adjust the verification protocol in order to reduce costs and avoid the data availability problem. Previously, transcoders were required to invoke Truebit verification for 1 out of every `VerificationRate` segments, which was set to 1 out of 1000 segments originally. This is very expensive, and is required whether the Transcoder did the work correctly or incorrectly. The new proposal is that:

* Broadcasters are responsible to verify received transcoded segments, and only challenge them to Truebit on chain if they believe that the segment failed verification.
* If Truebit (or other appropriate on chain verification function) agrees, then the Orchestrator’s stake is slashed, and the Broadcaster receives a significant bounty.

<img src="https://livepeer-dev.s3.amazonaws.com/docs/faultverification.jpg" alt="Fault Based Verificaiton">

Part of the argument against this method is that the Broadcaster doesn’t have significant compute resources to re-encode video to check whether the job was done correctly or not. Using the same randomized approach as the original protocol however, the Broadcaster can check 1 out of `VerificationRate` segments should it choose to. It could check more if it requires more reliability, or it could outsource the checking to another node on the network and pay that node to check efficiently on its behalf - the equivalent of hiring a second Orchestrator just for one out of `VerificationRate` segments. They could be using a cheap Orchestrator for the main work, but rely on the high reputation high cost Orchestrator as a more trusted verifier. There are also far cheaper checks that can be done by analyzing frames of the output video rather than fully re-encoding, such as metrics-based verification. These cheap checks can be used to test whether there is a likely fault, and only in that case then re-encode before bringing the challenge to Truebit.

The key point however, is that the Orchestrator doesn’t know which segments will be challenged, and should any segments fail verification, it stands to lose a tremendous amount of stake. The benefits of cheating would have to exceed the value of a fully slashed fixed stake deposit, which is unlikely for many use cases. Additionally, as Broadcasters may use redundancies, should it detect an inconsistency or have suspicion of cheating from a cheap check-without-re-encoding operation, it could simply choose to work with another Orchestrator on that segment in order to get a proper encoding and insert it into its playlist. 

One impact of this is that the cost of Truebit doesn’t need to be incurred, except in the case of obvious cheating - and hence almost never, since it should never be worth it for an orchestrator to intentionally cheat. This makes the network far cheaper to use, than the cost of invoking Truebit on every `verificationRate` segments of video. 


## Economic Analysis #################################

The changes proposed by Streamflow lead to slightly different incentives and behaviors for both Orchestrators and Delegators, resulting in what will be a more scalable, reliable, cost effective network. This section begins an economic impact analysis of these proposed changes, including a look at the role of the Livepeer Token, the role of delegation, how inflation effects the network, and some offchain economic considerations.

### Livepeer Token

The Livepeer Token (LPT) could always be described as a work token. Those who staked it had the opportunity to perform work on the network, and therefore earn the future fees (in ETH) for doing said work. Work was routed in direct proportion to stake, if prices offered by all nodes were constant. There were conceived mechanisms from the beginning for a “work requirement”, in that if a node did not perform enough work within some threshold proportion of their stake, then they could be slashed. This was an attempt at ensuring that nodes would actually contribute value (or incur overhead tax for not doing so or faking it), rather than just sit idly on stake and accrue inflation. In addition, there was no requirement that work be done cost effectively or in a performant manner. Competition could be socially encouraged, but not enforced at a protocol level.

The updates to the protocol to relax the artificially constrained number of Orchestrators, and the offchain job negotiation appear to change this direct connection between token and the right to do work on the surface, but upon further analysis, the same value accrues in an equilibrium state. Let’s look at the function that a token holder is attempting to maximize:

`Value accrued in a single round = inflationary LPT earned + fees earned.`

The inflationary LPT is predictable, based upon the rewardCut of an orchestrator. A Delegator can choose exactly how much LPT they would like to earn in exchange for the QA work they are doing. 

The fees earned on the other hand are less in control of the token holder. This is because it depends on: 

1. How much work their Orchestrator performs
2. The Orchestrator’s `feeShare`
3. How much total stake is delegated towards the Orchestrator, and therefore what percent of the fee pool they are entitled to

At the completion of a round, a Delegator will be able to calculate the earning power of their staked LPT. It’s this fee ratio:

`ETH in fees / unit of staked LPT`

Which will be the visible statistic that Delegators can use to compare Orchestrators to one another, and predictably, delegation should shift from round to round towards nodes where there is opportunity to maximize this ratio. In short, why stick with a node who’s sharing out 1gwei /  LPT staked when there’s another node you could switch to that is sharing out 2 gwei / LPT staked?

<img src="https://livepeer-dev.s3.amazonaws.com/docs/feeratio.jpg" alt="Fee Ratio">

But then it is worth noting that the act of switching more stake onto this opportunistic node, means that the fees will be split amongst more stake, and the fee ratio will decrease. The equilibrium state is that nodes who are performing more work (earning more) have more stake, and nodes performing less work with same fee share have less stake. Essentially all competitive nodes should end up with the same equilibrium fee ratios, with intelligently delegated stake earning a Delegator the equilibrium return - and hence staked LPT intelligently applied yields access to do work to earn fees on the network independently of how jobs are assigned.

### Delegation as Security and Reputational Signal

One negative outcome people could foresee is that nodes who are winning a lot of work could provide 0% fee share, and hence not attract any delegation. This is ok - they are running hardware and incurring costs, and providing great service to the network - they may not need delegation. But delegation on the other hand provides additional security - it is more stake that can be slashed if the node cheats - more reputational signal. Clients use this signal to select nodes to work with, and so a competitive node advertising a > 0% fee share would be more likely to attract stake, and hence work - as long as they can perform it competitively or better or cheaper than the 0% fee share node. Again, this contributes to the flexible setups and use cases of the network. It increases the opportunity for competition, decentralization, diversity, and resilience of the network.

As new nodes are looking to compete to do work on the network, they may need to attract enough stake to offer the security required by Broadcasters. In these cases, it is likely that these nodes would set a greater fee share. Active delegators will have the opportunity to search for and stake towards nodes that are winning outsized portions of works, with greater fee shares, resulting in higher fee ratios. In short, delegated stake can provide security and route work, in exchange for fees shared back when the work is performed well. Active delegation can lead to giving more opportunistic nodes the ability to expand the footprint and capabilities of the network in a competitive way.


### Inflation into Bonded State and Apathetic Delegators
One of the criticisms of the uncapped stake model with no minimum stakes is that it enables lazy behavior on behalf of the delegators. Inflationary LPT continues to accrue into the bonded state, continues to compound, and allows a delegator to set-it-and-forget-it while collecting LPT without adding significant value to the network. 

This may be the case in the very early days of the network, before fees serve as an additional incentive for delegators to take action, but is unlikely to yield a maximal result when Orchestrators are competing to do work, earn, and distribute fees. At this point, autopilot behavior may still lead to accruing LPT, but would be forgoing the potential fees that could be earned by switching to Orchestrators who are yielding a higher ETH/staked LPT ratio. 

As the inflation rate is likely to decrease under scaled usage, when token holders are staking to compete to earn the fees, the portion of the reward function that is accounted for by inflationary LPT also continues to decrease, with a great portion coming from fees. And as outlined above in the LPT section, the necessity to constantly QA the network and route work towards nodes who are outcompeting other nodes is financially motivated by the opportunistic returns. In short, an apathetic delegator is rewarded less than an active delegator.

Additionally, as Orchestrators who once needed to attract outside delegation in order to achieve the minimum stake, accrue enough stake themselves to secure their own node, they may decrease their fee share. At this stage, an optimizing delegator would be best served by seeking out a new up-and-coming node - essentially one who can expand the footprint of the network - who may be offering a higher fee share in order to attract stake. It's this constant QA performed by the optimizing Delegator, and stake-for-fee tradeoff which will create constant competition and further the decentralization of the network.

### Offchain Engineering Considerations
As previously mentioned, one of the core philosophies within Streamflow is to move many of the opinions about valid parameter values and p2p interactions out of the core protocol and into client implementations and configurations. Multiple implementations and configurations of these parameters will lead to a robust network that is resillient to attacks and malicious actors. However since the protocol itself is less opinionated, a lot is left up to client implementation. Here are some of the major considerations that need to be undertaken from an engineering perspective to make Streamflow work really effectively out of the box:

* PM risk management policies - when an Orchestrator should work with or not work with a Broadcaster based upon reputation and history, and vice versa.
* Secure random number generation for PM protocol.
* DDoS resistance for Orchestrators.
* Redundancy and failover algorithms for Broadcasters under different scenarios and use cases.
* Price discovery strategy for Broadcasters.
* Low latency streaming protocols and signature/payment verification when final segment isn't available before work needs to begin.

Each of the above can effect the efficiency of the network from the perspective of a Broadcaster - and hence the necessary redundancies, and eventually costs. The good news is that much of the above can be handled via off chain strategies, and can be constantly experimented with across different competing implementations or configurations. A network that has agents acting in different and unpredictable ways is harder to optimize for an attacker who would otherwise be looking to game a single implementation. 


## Attacks ###############################
Some of the specific sub-protocols, such as PM’s and Truebit based verification are subject to their own attacks, which we leave for analysis within those areas of research. Here is some brief discussion of potential attacks and countermeasures within the economics of the Streamflow changes to the protocol.

### Delegator Squeezing

When a candidate Orchestrator would like to operate a node and express their candidacy, they may need to attract delegation in order to reach the client specified minimum deposit amount to attract mainstream work. To do so, they may represent an attractive `RewardCut` and `FeeShare`. However, as their node begins to perform work, and they start to earn inflationary LPT, they may wish to use this LPT to stake more towards their single node in order to reduce the amount of inflation and fees they need to share with their delegators. To do so, they may drive off current delegators by manipulating their `RewardCut` and `FeeShare` to an unattractive point, and then filling the gap with their own stake.

This is theoretically ok, as delegators can move on to more attractive nodes and adjust in their best interest. Unfortunately, it creates an annoying UX, in that the delegators need to be constantly vigilant and active to operate in their best interest. Each round, shares may shift from under them.

One belief is that Orchestrators who would like to run additional nodes, maintain a positive reputation to attract significant delegation, and compete for fees, will have their reputation harmed by this approach and will not attract future delegation.

### Delegator Fee Theft

As mentioned in the Delegator Squeezing Attack above, it is possible for the Orchestrator to drive off its delegates. This could become a particularly malicious technique if the Orchestrator also holds onto its winning PM tickets until the point when the delegators leave, and then cashes them when it contains all the stake for its node. Essentially the fees and rewards that the delegates are entitled to would be delivered to the Orchestrator instead.

This can be counteracted by having expiration dates on the PM tickets, which occur prior to the withdrawal date on the Broadcasters time-locked deposits. As such, the tickets would need to be cashed in short order, and would potentially contain the committed fee share of the Orchestrator at the time, such that when cashing a winning ticket the appropriate splits could be made amongst delegators and the Orchestrator.


## Open Research Areas ############################

As with all work in the early field of blockchain based crypto economic protocols, there are still many research problems which need to be persued before the systems can achieve full decentralization, trustlessness, and economic efficiency. Here are a couple areas that the project is actively conducting research in. Community participation is welcome in pressing forward on these areas as well.

### Non Deterministic Verification

Work continues on the research to verify the likelihood that a GPU encoded segment represents the same content as the pre-encoded segment. This probabilistic and metrics driven approach has been shown in experiments and early research to yield accurate scores, however the suitability for actually slashing deposits based upon probabilistic outcomes is certainly debatable and requires further research.

Deterministic encoding on the other hand can continue to be checked by a variety of verification schemes including Truebit, SGX based hardware verification, Oracles, or even trusted verifiers.

### Public Transcoder Pool Protocols

The split between orchestration responsibilities and transcoder responsibilities should help to dramatically scale the operations of nodes on Livepeer, by leveraging idle hardware to transcode video, without necessarily requiring all those machines to be Livepeer aware 24/7 operating, staked nodes. It is believed that private pools, where the Orchestrator also contains this transcoding hardware, will be the most cost effective, because the Orchestrator can trust that the result that comes out of the transcoders is correct and not malicious.

Public pools, where the orchestrator doesn't trust the transcoders, but can allow anyone to opt in to race to transcode segments, could be very powerful at leveraging any idle compute, without having to have dedicated infrastructure oneself. However, since these remote transcoding nodes aren't trusted, the Orchestrator would have to check their work, or else risk being slashed. This incurs additional costs, and therefore may be unlikely to compete with private pools - unless economic protocols can be created to secure these public pools in the form of staking deposits. If it can be shown that a particular unknown transcoder was the result of a slashing condition being invoked, and they have enough of a deposit/stake to cover the cost of the slash, then public pools could be viable.

Further research and design here is an open topic.

### Broadcaster Doublespend Mitigation

In a probabilistic micropayments scheme, there is always a chance with some probability that a Broadcaster has issued more winning tickets than they have balance to pay (accidentally). And since Orchestrators may not notify the Broadcaster of a winning ticket immediately, it is hard to get an accurate accounting of a Broadcaster's balance. We're continuing research on the required parameters and deposit management to avoid an accidental double spend under various usage patterns in the network. See further analysis in the Probabilistic Micropayments appendix.

### VOD Payments

One of the nice properties that Broadcasters may look for when it comes to video-on-demand transcoding is the notion that they can make the content available, request a job, and disappear - such that the Orchestrator can perform the job asynchronously, distribute it across many nodes, or schedule it when they have idle resources available.

However, in the PM scheme described in Streamflow, the Broadcaster needs to be online in order to continuously send payments as the content streams. Part of the security is in the recognition that if an Orchestrator doesn't continue doing the work, it's ok, as the Broadcaster will simply stop sending future payments.

For VOD jobs though, if a Broadcaster pays up front for all segments of video and then disappears offline, there is no security to guarantee that the Orchestrator will perform the transcoding or make the transcoded segments available back to the Broadcaster. For now, VOD transcoding is possible, but upload-and-disappear is not. Research will continue on better mechanisms to enable VOD payments.

## Migration Path ############################

The Streamflow proposal is early on in its research, design, feedback, and implementation cycle. It certainly deserves a thorough community critique, testing, audits, and acceptance prior to going live on the Ethereum main net as the next iteration on top of Livepeer's alpha protocol. This section aims to list out a couple early considerations with regards to how a protocol migration could occur:

* New smart contract logic would be deployed to Ethereum, however it is anticipated that very little to no data migration would be necessary. Livepeer's existing proxy-delegatecall update mechanism could be utilized.
* Existing state in the Livepeer protocol including staking balances, fees, rewards, delegation, etc would be maintained.
* Transcoders, Broadcasters, Orchestrators, and Delegators would update their client software, which would contain logic for job negotiation, redundancy, payments, and updated verification.
* Orchestrators would register any new required parameters to the service registry, including supported services and possibly locations.
* Broadcasters would establish PM contracts and deposits. Existing deposits could migrate from the Minter to the PM contract via user driven action whenever requested.
* It is anticipated this could be accomplished with little-to-no downtime to the protocol.
* 3rd party clients such as protocol explorers and analytics tools would likely need updates in order to reflect the new protocol interactions.

A formal migration path, checklist, and multiple observed testnet runs will be made available over time as the candidate Streamflow release date nears.

## Summary #################################

In conclusion, the proposals contained within this document aim to shine a light on a scalable path for Livepeer's video infrastructure network - one that decouples the cost of using the Ethereum blockchain from the cost of using the network itself, and that provides existing scaled video developers with the reliability and performance that they require from their infrastructure.

All feedback, ideas, and input are welcomed, so please do not hesitate to drop into [The Livepeer Forum](https://forum.livepeer.org) or [Discord Chat](https://discord.gg/RR4kFAh) to participate.


## Appendix ################################

### Appendix A: Probabilistic Micropayments Workflow

* An orchestrators security deposit is their stake. This can get slashed if they cheat and fail a verification.
* A broadcaster (using this term for general user, which may be more of a developer than broadcaster) places a time-locked deposit to cover the future work that they’ll pay for on the network.
* A broadcaster wants video transcoded. They look at the on chain registry of orchestrators advertising their services, and negotiate off chain with the ones that fit their needs:
    * Orchestrators provide them a price quote.
    * Orchestrators provide probabilistic micropayment (PM) parameters - these can vary depending on Ethereum network conditions. For example they can set the winning ticket amount such that the cost of cashing in is less than 1% of the value received.
* Broadcaster sends segments of video to the orchestrator(s) they want to work with along with PM ticket.
    * PM ticket is an interactive protocol in order to prevent either party from biasing the source of randomness used to determine whether a ticket wins or not - after every winning ticket, the orchestrator needs to generate a new random # and send the commitment to the broadcaster. This is probably ok since the broadcaster and orchestrator will already be sending data back and forth already - this would just entail an additional message sent by the orchestrator every time a new commitment is required. A later optimization that might be possible is the use of a verifiable random function (VRF) implemented in a smart contract - the orchestrator would give the broadcaster a pub key and the orchestrator signs received tickets with the corresponding priv key - the VRF contract would verify that the sig is correct which is then used as the source of pseudorandomness. As a result, the protocol becomes non-interactive. Would need to evaluate feasibility of implementing the VRF in a smart contract and the cost of verifying that type of sig - ok not to focus on this right now, but a possibility for the future.
* Broadcaster receives transcoded output back from orchestrator.
    * Broadcaster can verify any segments it wants to check.
    * If the work doesn’t verify, they can provide this proof to Truebit on chain to slash the orchestrator, and earn massive reward.
* If broadcaster doesn’t receive work back from orchestrator, simply stop sending them future segments and work with different orchestrator.
* If the orchestrator doesn’t receive a valid PM ticket, just don’t do the work and don’t send any output back.
    * The protocol will contain messages for certain error conditions, such as `LowPMBalance` or `SegmentFormatDidntMatchJobInputParams` so that the broadcasters can receive some useful information to debug, or so that their node can make decisions like refilling their balance.
* Orchestrator monitors broadcaster’s deposit and assesses risk of default.
    * Simple algorithm to begin with. If their balance is too low, just stop doing work.
    * Orchestrator cashes winning tickets as they’re received (or waits until gas is cheaper, assessing risk of default).
    
For a full analysis and specification of the ticket data structures, double spend prevention, and other design considerations, see this [external document](https://hackmd.io/uHMFeNSyS_GyzwnO3Ld74A?view).

## References ###########################################

1. Livepeer Whitepaper - Doug Petkanics, Eric Tang - <https://github.com/livepeer/wiki/blob/master/WHITEPAPER.md>
2. The Video Miner, A Path To Scaling Video Transcoding -Philipp Angele  - <https://medium.com/livepeer-blog/the-video-miner-a-path-to-scaling-video-transcoding-a3487d232a1>
3. Ethereum Probabilistic Micropayments - Gustav Simonson - <https://medium.com/@gustav.simonsson/ethereum-probabilistic-micropayments-ae6e6cd85a06>
4. Electronic Lottery Tickets as Micropayments - Ron Rivest - MIT Lab for Computer Science - <https://people.csail.mit.edu/rivest/pubs/Riv97b.pdf>
5. Decentralized Anonymous Micropayments - A. Chiesa, M. Green, J. Liu, P. Miao, I. Miers and P. Mishra - <https://eprint.iacr.org/2016/1033.pdf>


