# ZBadges enable digitally scarce or achievement-based badges on the Zilliqa Blockchain

As part of a potential future ZRC, this repository contains prototype code for the creation of scilla contracts conforming to a badge standard delegating authority to manage membership in badgeholder pools. This pattern is intended to serve as a flexible alterantive to full-fledged ACLs or more complex and difficult to understand reputation systems. It should be suitable for games, simple DAOs, decentralized project management. The reference errs on the side of being too short because much of the logic will change for these more complex implementations. 

## Context

The Ethereum equivalent to this effort, [OpenProofs](https://medium.com/the-spectrum/open-proofs-an-open-source-badge-standard-477906d001f3) has been inactive for some time. Their [implementation](https://github.com/rrecuero/open-proofs) has not been updated in some time. It is based on the ERC-721 standard, with the addition of some metadata and a lot of logic around organizing communities and designating badge data such as title, image, etc. 

## Redesign

This implementation is designed with the inherit modularity of Zilliqa in mind. Any large-scale badge standard would be bogged down if it all ran through one contract on their sharded chain, so we will change the conceptual structure somewhat. This contract represents a single authortative badge, meaning it can be held by multiple members, but as a unified group of 'badge holders' granted that badge under the same authority. It might have multiple methods for issuance or an upstream contract that manages authority, but, its badges issued are fully represented by the deployed .scilla contract. In order to have a 'community', you might combine this contract with a multisig wallet and an off-chain (ex: IPFS) data store. The reference implementation assumes just that.

Only an authority can grant a badge, but in order to do so it must be voluntary. Thus a badge requester must send a metatransaction to the badge authority, which then approves or rejects the request by either sending it to the chain with the authority signature or discarding it. This design for a standard ensures that users aren't marked against their will. 
Once marked with a badge, it is non-transferrable. A user may still choose to burn the badge, again, with the authority's permission. 

## Scenario

Consider that you are a local `Recon Girl` `Troop Leader`. A `Recon Girl`, much like a Girl Scout, wants to sell cookies to earn an achievement badge that distinguishes her among her peers. In this scenario, the Recon Girls are as decentralized as possible - no central organization exists above the `Troop Leader`, and for that reason they issue their badges on the blockchain, so that different `Troops` can compete and compare. The Badge in this case, is for selling 5 boxes of cookies. Each girl gets a badge once she sells her fifth box of cookies. This scenario can cheaply and fairly be fairly managed on the Zilliqa blockchain, so how can we put this badge there and give fair access across troops?

The above scenario hopefully justifies breaking out the excessive logic for "communities" in the OpenProofs contracts - "communities" are too homogeneous, and not always present in every badge application. 

To complete the scenario, we could have a second badge class is created that is the governance for all the leaders of all the Girl Scout Troops, it would have a minimum interface including a `Global Leader` role, an active member roster and a mapping of which other agents in the Scout Troop ecosystem have achieved the right to be `Troop Leaders`. The mapping would point each `Troop Leader` to their associated badge contract, and the subordinate badge contracts could even set the signing authority according to the master ZBadge that designates the valid `Troop Leader`. Later, this can be extended with each troop's preferred governance system acting as a proxy contract to the ZBadge,  but to interact with the decentralized badge-market they would only need the standard described and an additional field for `subscriptions` reporting the badges each troop unit has bought into.


