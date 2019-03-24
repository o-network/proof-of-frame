# Proof of Frame

## The Problem

With the current techonologies available, there is no way to prove that a frame of information is from a trusted source, whether it be a picture, video, set of statistical data, or a electoral vote. 

A great way of verifying these frames is to upload a hash of said frame to a distributed ledger which has proof of work associated to adding transactions to the ledger, however this requires that frame verification relies directly on a physical time exchange (by way of proof of work). 

To allow for frame verification to be indpendent of physical time we need to first produce a trust exchange between two or more ledgers, with one ledger verifying directly through another ledger. 

We can produce this trust exchange process in a way where the dependent ledger can define the level of trust they require before utilisng the exchanged trust information. 

## The Solution

This can be solved by creating a ledger and routinely submitting the most recent hash to a public proof of frame or proof of work chain. 

Auditors can then use the publicly submitted hashes to verify the ledger has not been tampered with.

Anyone with the ledger source can prove that a frame of information is from a trusted chain of events and has followed best
practices along the way. 

##  Ledger

In this document a ledger is a set of events where previous events cannot be tampered with without changing the current
state of the ledger.

#### Eventual trust

A trust exchange binds the ledgers state at a specific point in time, a ledger can record multiple events over 
a period of time without creating a trust exchange with another ledger. 

Some ledgers may be offline for a large period of physical time, meaning it might not be possible for a ledger to 
achive a higher level of trust. 

To provide absolute performance to agents utilising a ledger, it would be ideal if a hash of a frame can be appendend 
at will to the head of any ledger, changing the state of the ledger, and decreasing the trust level of a ledger. 

When the ledger takes part in a trust exchange with any level of required trust, the only thing that is required
to provide is the head state of the ledger, which is recorded in the independent ledger. 

#### Privacy 

A ledger never needs to be distributed publicly, only trust exchanges are public, and they can be achieved by providing 
an intermediary ledger that can be held either offline or online, when verification is required only the verifying parties
need to have access to the source ledger, and can compare the trust exchanges with the trusted ledger.

#### Frame information

A frame can be any information that may require verification, for example a broadcast of a political event should be verifiable by comparing the frame received and the publicly trusted frames originally broadcasted. 

From a techical point of view, a frame is a set of bytes that can be [hashed](https://en.wikipedia.org/wiki/Hash_function) to produce a "Hash Frame" which can be appended to the head of a ledger 

### Levels of trust

Below are some _examples_ of level of trust that can be had

#### Single trusted ledger acceptance

A trust exchange could be between a dependent ledger and an independent ledger,
this could be done between two ledgers in close physical locality for example.  

#### Eventually consistent ledger

A trust exchange could be between a dependent ledger and an independent distributed ledger, 
the depending ledger may request a certian number of trusted ledgers accept the 
exchange is valid, or request a proof of work be completed in a distributed token economy.

#### Proof of work legder

A proof of work ledger follows the same rules as a conventional distributed token economy, for example [bitcoin](https://bitcoin.org/bitcoin.pdf)

#### Mix

We can have a mix of trust depending on the level of trust required for each ledger

## Example

Say we have a `Party` class and a `Ledger` class, the party can accept either a private or public key, 
if the party has a private key it will have a writable ledger, if it is public, they will only be able to 
read frames from the ledger for verifications. 

The `Party` class will have one function, `exchange` which will be available for public parties. 

The `exchange` method accepts a `Frame` which has the type of `trust-exchange` or `trust-acceptance`

### Client

```js

const privateParty = new Party(process.env.PRIVATE_KEY, process.env.PUBLIC_KEY);
const privateLedger = new Ledger(privateParty, "https://example.com/ledgers/private");

const trustedParty = new Party(process.env.TRUSTED_PUBLIC_KEY);

const nonce = await privateLedger.nonce();
const newFrame = {
	type: "trust-exchange",
	nonce,
	// This also creates a new hash entry in the ledger, ensuring this newFrame will contain a record of this
	// hash being geniune 
	hash: await privateLedger.hash(nonce),
	targetIdentifier: trustedParty.identifier,
	timestamp: Date.now()
};

// Appended frame will contain a signature 
// If the ledger didn't yet contain the public key, it will be appended as a "public-key-acceptance" frame before signing with the 
// private key
const appendedFrame = await privateLedger.append(newFrame);
const responseFrame = await trustedParty.exchange(appendedFrame);
// Will overwrite the signature and previous hash with the private ledger details, but hash & other details will be the same
await privateLedger.append(responseFrame);
```

### Trusted Party

```js
const privateParty = new Party(process.env.PRIVATE_KEY, process.env.PUBLIC_KEY);
const publicLedger = new Ledger(privateParty, "https://example.com/ledgers/public");

privateParty.on("frame", async (sourceIdentifier, frame) => {
	if (frame.type !== "trust-exchange") {
		throw new RangeError("Invalid type received 'trust-exchange'");s
	}
	// Do some verification of the signature etc here
	// Once verified we can append a new frame
	const newFrame = {
		type: "trust-acceptance",
		hash: await privateLedger.hash(nonce),
	    sourceHash: frame.hash,
	    sourceIdentifier: sourceIdentifier,
	    nonce: frame.nonce,
	    timestamp: Date.now()
	};
	return publicLedger.append(newFrame);
});
```

## Contributing

Please see [Contributing](./CONTRIBUTING.md)

## Code of Conduct 

This project and everyone participating in it is governed by the [Code of Conduct listed here](./CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code. Please report unacceptable behavior to [conduct@open-network.dev](mailto:conduct@open-network.dev).

## Licence

The written documentation and source code examples are licensed under the [CC0 1.0 Universal](https://creativecommons.org/publicdomain/zero/1.0/) license.