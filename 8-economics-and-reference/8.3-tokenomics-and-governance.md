# 8 3 tokenomics governance

## 8.3.1 MEMO, Staking and the Settlement Layer

The economic layer connects users, storage providers, guardians / verifiers, compute services and protocol governance along "service → proof → settlement". A service order defines quantity, quality, proof window, fees and compensation conditions; meters and verifiers output reconcilable records, and the settler carries over revenue, staking and penalties according to the contract parameters.

Settlement assets are defined by an Asset Descriptor specifying symbol, type, network, contract, precision, accepted scope and fee rules, and are bound through signatures and on-chain code validation. Gateway selects a payment path based on the assets accepted in the quote, and resource access still depends on an independent delegation. Asset descriptions, user permissions and economic allocation belong to different configuration domains.

_Figure 29: Relationship Among Service Fees, Node Contribution, Staking and Settlement_

Staking raises the cost of dereliction or misbehavior, while technical proofs, redundancy and recovery mechanisms bear the responsibilities of service verification and reliability. Rewards are allocated based on verifiable capacity, storage responses, valid challenges, repair results and controlled computation evidence, combined with the metering of real service demand. Node correlation, fake traffic, self-dealing and duplicate proofs are identified through deduplication, correlation analysis and challenge rules.

Account balances, direct on-chain settlement, x402 and enterprise billing are connected to the unified ledger through four kinds of payment adapters. Fees are associated with quotes, Owner authorization, `operation_id`, service evidence and refund or dispute status. The public chain keeps the necessary settlement information, and task plaintext stays off-chain; the disclosure scope of addresses, amounts and times is controlled by privacy configuration, and linkage risk enters the audit policy.

## 8.3.2 Allocation, Incentives and Cold Start

Economic allocation uses a parameterized model. Let the allocation base be `S = 300,000,000`, with shares computed as in the table below; supply, allocation, release and vesting rules are bound to the signed economic configuration and the executing contracts, and the calculator checks the sum of shares, asset precision and budget caps.

| Allocation item                | Model ratio | Amount computed from S | Enforcement rules                                                      |
| ------------------------------ | ----------- | ---------------------- | ---------------------------------------------------------------------- |
| Allocation base                | 100%        | 300,000,000            | Configuration parameter `S`, used for budgeting and release simulation |
| Foundation                     | 15%         | 45,000,000             | Treasury purposes, receiving account, lock-up and spending policy      |
| Investors                      | 10%         | 30,000,000             | Vesting curve, unlock conditions and receiving account                 |
| Liquidity                      | 15%         | 45,000,000             | Market scope, quota, accounts and transfer rules                       |
| Ecosystem / storage incentives | 60%         | 180,000,000            | Service performance, release curve, reward cap and anti-abuse policy   |

The economic configuration declares the supply control method, allocation accounts, vesting and release functions, inflation or burn rules, treasury permissions, pause conditions and change time locks. Allocation amounts satisfy `allocation_i = S × ratio_i`, and the four allocation ratios sum to 100%; release calculation takes the available quota according to time and performance conditions, and exceeding the corresponding budget is prohibited. Model output is used for configuration simulation, rule validation and ledger reconciliation.

Cold-start mechanisms are divided into three incentive configurations: process validation, capacity bootstrapping and demand-driven. Process validation uses test assets without value; capacity bootstrapping sets caps on nodes, capacity and term, and releases rewards based on service and proof records; demand-driven allocation includes actual service revenue in the distribution and adjusts the incentive budget by subsidy ratio and demand threshold. Each configuration performs Sybil attack, node correlation, fake order and self-dealing detection, and triggers reduction or suspension of subsidies by rule. Agent state incentives are associated with recoverable state, effective handoffs, proofs and service quality, and rewards are not determined by write volume alone.

## 8.3.3 Node Economics, Challenges and Governance

Node responsibility forms an operating loop of "register / stake → accept orders → provide service → submit proofs → challenge and repair → settle or penalize". The protocol configuration binds node eligibility, challenge rules, proof windows, service fees and exit responsibility; the settler executes the corresponding rules on the basis of service records and challenge results.

_Figure 30: Service, Proof, Repair and Exit States of a Storage Node_

The challenge configuration includes sampling frequency, response window, proof algorithm, evidence retention period, review and appeal rules. A monitor records failure rates, recovery time, and geographic and operator concentration; simulation uses these inputs to assess false penalties, attack cost and recovery load. Anomalies enter the challenge and review flow, and network fluctuations, repairable faults and fraud are handled under different conditions.

The governance configuration covers minimum staking, rewards and penalties, proof windows, algorithm sets, network upgrades and treasury spending. Changes use versioned governance proposals and execute in turn rule validation, simulation, the signature or voting threshold set by the charter configuration, a time lock and post-execution state reconciliation. The emergency response role is given only the minimum permissions needed for vulnerabilities and systemic failures, its authorization carries a term, scope and audit record, and using emergency permissions to modify unrelated assets or user authorizations is prohibited.

The public disclosure scope of runtime evidence, the default retention period, cross-organization handoff rules, Attestation issuer admission, the controlled computation trust list and assetization conditions are each configured separately. Changes involving privacy and data rights must meet preset role and policy thresholds, and no single economic weight can override the Owner's object rights and purpose limits. Policy enforcement points load the configuration after verifying the governance version, scope and effective time.
