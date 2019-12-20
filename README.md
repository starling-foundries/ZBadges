# ZBadges enable digitally scarce or achievement-based badges on the Zilliqa Blockchain

As part of a potential ZRC-4, this repository contains prototype code for the creation of scilla contracts conforming to a badge standard delegating authority to manage membership in badgeholder pools. This pattern is intended to serve as a flexible alterantive to full-fledged ACLs or more complex and difficult to understand reputation systems. It should be suitable for games, simple DAOs, decentralized project management. The reference errs on the side of being too short because much of the logic will change for these more complex implementations. 

## Context

The Ethereum equivalent to this effort, [OpenProofs](https://medium.com/the-spectrum/open-proofs-an-open-source-badge-standard-477906d001f3) has been inactive for some time. Their [implementation](https://github.com/rrecuero/open-proofs) has not been updated in some time. It is based on the ERC-721 standard, with the addition of some metadata and a lot of logic around organizing communities and designating badge data such as title, image, etc. 

## Redesign

This implementation is designed with the inherit modularity of Zilliqa in mind. Any large-scale badge standard would be bogged down if it all ran through one contract on their sharded chain, so we will change the conceptual structure somewhat. This contract represent a single instance of a badge. It might have multiple holders, it can even be modified to have multiple authorities. But at the end of the day, its badge classes are "colors" or "flavors" of the badge represented by the deployed .scilla contract. In order to have a 'community', you might combine this contract with a multisig wallet and an off-chain (ex: IPFS) data store. The reference implementation assumes just that.

## Scenario

Consider that you are a local `Recon Girl` `Troop Leader`. A `Recon Girl`, much like a Girl Scout, wants to sell cookies to earn an achievement badge that distinguishes her among her peers. In this scenario, the Recon Girls are as decentralized as possible - no central organization exists above the `Troop Leader`, and for that reason they issue their badges on the blockchain, so that different `Troops` can compete and compare. The Badge in this case, is for selling 5 boxes of cookies. Each girl gets a badge for _each_ 5 cookies she sells, but when she gets to 20 badges, her small badges are automatically upgraded to a larger badge. If she sells enough cookies to get 100 badges, then she gets a huge prize. This scenario can only be fairly managed on the blockchain, so how can we put this badge there and give fair access across troops?

To accomplish this, we would:
* clone this repository 
* run the `authority` service on a publicly addressable server somewhere
* deploy the ZBadge.scilla contract
* setup a signing keypair on the `authority` server, use the `owner` to set that keypair as the Authority 
* `Troop Leaders` submit requests to issue badges to the authority contract, perhaps hashing 5 cookie TX reciepts to validate
* Approved requests result in `Recon Girls` seeing new badges in their badge inventory once processed on-chain.


This is interesting enough, but let's say that the badges should compete in a market, where new badges can be proposed by `Troop Leaders` and adoption by other `Troop Leaders` results in the badge going up in ranking, if badges go below a certain rating they could be retired. 

The above scenario hopefully justifies breaking out the excessive logic for "communities" in the OpenProofs contracts - "communities" are too homogeneous, and not always present in every badge application. 

To complete the scenario, a second contract class is created that is the governance for a Girl Scout Troop, it would have a minimum interface including a `Troop Leader` role, an active member roster and a list of badges the troop "subscribes" to as a unit. This can be extended with each troop's preferred governance system, but to interact with the decentralized badge-market they would only need the standard described and an additional field for `subscriptions` reporting the badges each troop unit has bought into.


