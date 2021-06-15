# ITIP-[xx]
*Using template v0.1*
## Abstract
Index Coop and other partners will be seeking to launch numerous portfolio strategies that need to be periodically rebalanced. Currently, these are FLI, DPI and MVI with TTI, BED, SDI, Stable Yield Index in the pipeline.

To successfully handle this load, IC will need to scale, operationalize and automate these rebalances. Historically, IC has spent 2-4 person days every month (Brian, Alex, Dylan) rebalancing 2 existing indices (DPI, MVI). Most hours are spent setting up the rebalances (receiving weights from methodologists, executing multisig transactions, calculating new units, and parameterizing rebalances) which are manual tasks at the moment.

## Motivation
This feature looks to automate the rebalance unit calculation process and helps Index Coop:
1. Further decentralize the rebalance process
2. Streamline rebalances and reduce maintenance hours for IC treasury
3. Prepare to scale and handle 10+ index products
  * Minimize Multisig transactions to start rebalance
  * Horizontally Scalable: handle numerous Sets, various frequencies of rebalances, with different assets / liquidity profiles, across exchanges
  * Minimize rebalance parameterization
  * Minimize methodologist <> Set interactions

This feature will eliminate the need for the multisig to kick off rebalances for index products and improve the UX for methodologists to interact with the system during monthly / weekly rebalances using the GeneralIndexModule ("GIM")

This should work for our existing products (DPI, MVI) and new index products in the pipeline (SMI, BED).

## Background Information
The existing index products utilize the `ICManager` contract and Set Protocol's `GeneralIndexModule`. This is less generalizable / decentralized than the `BaseManager` and `BaseAdapter` system utilized by the FLI suite of products. The introduction of this `IndexRebalanceAdapter` will allow Index Coop to use new system with a `BaseManager`. Additionally, the `FeeSplitAdapter` can be reused to collect streaming fees vs having that functionality in the old `ICManager`

The `FlexibleLeverageStrategyAdapter` uses Chainlink oracles to price slippage and calculate the amount to rebalance. Similarly, we need to use oracles to translate % weights to SetToken units to pass into the GIM `startRebalance` function and price slippage for trades to prevent MEV.

Below is an audit of our current rebalance process for various products:
![Process Analysis](../assets/rebalanceProcessAudit.png)

## Open Questions
- [x] Can we generalize to use any oracle system other than Chainlink?
    - Yes, using the Set oracles adapter interface

## Feasibility Analysis

### Custom index rebalance adapter for each product
This option implements a custom adapter for each index product and encodes the methodology directly in the smart contracts. This is the most decentralized, but also the least flexible option. For DPI, this means encoding the circulation supply calculation and max % weight of 25% in the smart contracts. For SMI, this will directly read the debt pool.

### General rebalance adapter that is standard across indices
This option standardizes methodologist process to only providing % weights. The methodology is still calculated offchain (or in a separate smart contract for onchain data - potentially SMI). The contract will allowlist submitters who are only allowed by the operator and methodologist to submit % weights depending on the reblanace interval. This is then timelocked based on the methodology. For DPI, this is 7 days.

|      | Custom index adapter                                                      | General rebalance adapter                                                        |
|------|---------------------------------------------------------------------------|----------------------------------------------------------------------------------|
| Pros | Most decentralized, most automated                                        | Can be used across products, standardizes methodologist to operator relationship |
| Cons | Not flexible, need a new adapter for methodology changes and each product | Methodology is calculated offchain so more centralization there 

## Timeline
- Spec + review: 3-4 days
- Implementation: 4-5 days
- Internal review: 2 days
- Deployment scripts: 1 day
- Deploy to testnet: 1 day
- Testing: 3-5 days
- Write docs: 1-2 days

## Checkpoint 1
Before more in depth design of the contract flows lets make sure that all the work done to this point has been exhaustive. It should be clear what we're doing, why, and for who. All necessary information on external protocols should be gathered and potential solutions considered. At this point we should be in alignment with product on the non-technical requirements for this feature. It is up to the reviewer to determine whether we move onto the next step.

**Reviewer**:

## Proposed Architecture Changes
A diagram would be helpful here to see where new feature slot into the system. Additionally a brief description of any new contracts is helpful.
## Requirements
These should be a distillation of the previous two sections taking into account the decided upon high-level implementation. Each flow should have high level requirements taking into account the needs of participants in the flow (users, managers, market makers, app devs, etc) 
## User Flows
- Highlight *each* external flow enabled by this feature. It's helpful to use diagrams (add them to the `assets` folder). Examples can be very helpful, make sure to highlight *who* is initiating this flow, *when* and *why*. A reviewer should be able to pick out what requirements are being covered by this flow.
## Checkpoint 2
Before we spec out the contract(s) in depth we want to make sure that we are aligned on all the technical requirements and flows for contract interaction. Again the who, what, when, why should be clearly illuminated for each flow. It is up to the reviewer to determine whether we move onto the next step.

**Reviewer**:

Reviewer: []
## Specification
### [Contract Name]
#### Inheritance
- List inherited contracts
#### Structs
| Type 	| Name 	| Description 	|
|------	|------	|-------------	|
|address|manager|Address of the manager|
|uint256|iterations|Number of times manager has called contract|  
#### Constants
| Type 	| Name 	| Description 	| Value 	|
|------	|------	|-------------	|-------	|
|uint256|ONE    | The number one| 1       	|
#### Public Variables
| Type 	| Name 	| Description 	|
|------	|------	|-------------	|
|uint256|hodlers|Number of holders of this token|
#### Modifiers
> onlyManager(SetToken _setToken)
#### Functions
> issue(SetToken _setToken, uint256 quantity) external
- Pseudo code
## Checkpoint 3
Before we move onto the implementation phase we want to make sure that we are aligned on the spec. All contracts should be specced out, their state and external function signatures should be defined. For more complex contracts, internal function definition is preferred in order to align on proper abstractions. Reviewer should take care to make sure that all stake holders (product, app engineering) have their needs met in this stage.

**Reviewer**:

## Implementation
[Link to implementation PR]()
## Documentation
[Link to Documentation on feature]()
## Deployment
[Link to Deployment script PR]()  
[Link to Deploy outputs PR]()