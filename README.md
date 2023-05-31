# Livepeer Wiki

## Getting Started

If you are interested in developing video applications, running an orchestrator node or staking/delegating check out the [documentation](https://docs.livepeer.org/).

If you are interested in a community curated list of Livepeer applications and tools check out the [awesome-livepeer](https://github.com/livepeer/awesome-livepeer) page.

## Learn More

If you want to learn more about the current state of the protocol and network, then we recommend doing the following:

1. Review the [whitepaper](https://github.com/livepeer/wiki/blob/master/WHITEPAPER.md) for the original proposal behind the protocol and network.
2. Review the proposals that have updated the protocol since genesis and for historical context behind design decisions.
	- The protocol is updated via [Livepeer Improvement Proposals (LIPs)](https://github.com/livepeer/LIPs).
	- An exception is the [Streamflow proposal](https://github.com/livepeer/wiki/blob/master/STREAMFLOW.md) which was introduced prior to formalization of the LIP process within the community. 
	- The most significant proposals in terms of number of changes to the protocol include:
		- The [Streamflow proposal](https://github.com/livepeer/wiki/blob/master/STREAMFLOW.md) which introduced a variety of network scalability improvements including a service registry, offchain job negotiation and payment mechanisms, the architectural decoupling of orchestrator and transcoder nodes and an increased number of nodes supported.
		- [LIP-73: Confluence - Arbitrum One Migration](https://github.com/livepeer/LIPs/blob/master/LIPs/LIP-73.md) which dramatically reduced the gas costs of core protocol transactions by migrating core protocol contracts to the Arbitrum One rollup.
3. Review the smart contracts deployed to the Ethereum blockchain and the Arbitrum One rollup.
	- [Core protocol contracts](https://github.com/livepeer/protocol)
	- [Arbitrum bridge contracts](https://github.com/livepeer/arbitrum-lpt-bridge)
4. Review [go-livepeer](https://github.com/livepeer/go-livepeer/tree/master), a Go implementation of a Livepeer node.
5. Review the specifications to understand low level details.
	- [Core protocol spec](https://github.com/livepeer/wiki/blob/master/spec/streamflow/spec.md)
	- [Probabilistic micropayments spec](https://github.com/livepeer/wiki/blob/master/spec/streamflow/pm.md)