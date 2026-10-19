# 6 1 protocol mefs

## 6.1.1 Six-Layer Protocol and Network Architecture

MEMO Protocol contains six capability domains: data storage, computation, verification, execution, identity and intelligence. The Agent State Layer organizes upper-layer flows by the responsibilities of a state request; the two layerings express responsibilities at different levels and are not directly interchangeable implementation modules.

The network can be understood in terms of three kinds of responsibility:

* **Settlement layer**: role registration, group management, staking and fee settlement;
* **Data management / verification layer**: aggregating order information, challenges, credentials and repair scheduling;
* **Storage layer**: storing encrypted data and responding to read and proof requests.

Vault submits encrypted state objects through the MEFS backend adapter, which maps object versions, content envelopes, storage orders and lifecycle; Gateway performs identity and policy checks on requests at the managed entry point and calls Protocol's identity and execution interfaces; Proof connects to the verification, challenge and on-chain anchoring interfaces. The file storage service uses an independent versioned file interface, on top of which the state service adds object, delegation and evidence semantics. The three planes make the cross-component trust relationships explicit through independent credentials, restricted service accounts and call auditing.

## 6.1.2 Users, Providers, Guardians and Nodes

For node roles, guardians organize challenges and trigger the corresponding settlement based on verifiable results, so that payment is associated with protocol credentials. The network configuration pins role permissions, group members, challenge periods, verification thresholds, dispute windows and settlement rules; verifiers check signatures, randomness sources, time windows and duplicate challenges. The division of roles does not by itself guarantee the independence of participants, and the risks of collusion and centralization are constrained through participant isolation, rotation policies, cross-verification and time-limited dispute processes.

| Role                      | Network responsibility                                                     | Responsibility in Agent state scenarios                                                                | Key risks                                                    |
| ------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------ |
| User                      | Submits data, pays fees, requests reads                                    | State owner, originator of delegations, fee payer                                                      | Key loss, mis-authorization, insufficient fees               |
| Provider                  | Provides storage and responds to challenges                                | Stores encrypted state blocks, object versions and handoff packages                                    | Going offline, proof failure, data unreachable               |
| Guardian                  | Data management, matching, challenges, credentials and repair coordination | Coordinates state storage orders, challenges and repair                                                | Centralization, wrong scheduling, long-term unresponsiveness |
| Light / verification node | Verifies proofs or initiates challenges under the corresponding protocol   | Verifies Proof references, challenge results and storage status according to the configured proof type | Wrong challenges, version inconsistency                      |

Nodes start with a versioned deployment configuration that contains the network identifier, role, hardware resources, system requirements, ports, accounts, staking and balance thresholds, fees and health checks. The launcher automatically validates the network and contract code identifiers, resource capacity, balance and role eligibility; on a failed check it remains isolated. Stopping, upgrading and rolling back use separate draining, snapshot, version validation and recovery steps.

The network governance configuration separately specifies the role concurrency matrix, group admission and exit, public node access, sponsored quota and test asset distribution. When a node joins, the configuration signature, role conflicts and eligibility are validated; on exit, orders are settled, a controlled data handoff is completed and runtime credentials are revoked.

## 6.1.3 Redundancy, Repair, RAFI and Storage Proofs

MEFS's reliability design uses redundancy methods such as replication and erasure coding, and links data retention responsibility with node incentives through challenges, proofs, repair and settlement. RAFI (Risk-Aware Failure Identification) applies differentiated identification thresholds to stripes of different failure scales, prioritizing the identification and repair of higher-risk data blocks. This mechanism links failure identification with repair scheduling: priorities are set according to a stripe's redundancy margin, failure distribution and proof failure records, and stripes close to the fault tolerance boundary are restored first. Telemetry records identification latency, repair traffic, unreachable windows and recovery results by algorithm version, workload and failure model, and is used to adjust thresholds and resource quotas.

_Figure 22: Relationship Among Sharded Storage, Node Challenges and RAFI Repair_

Agent state adds requirements for versions and lifecycle: a proof must bind `object_id`, `version`, the algorithm and scope of the `commitment`, and the applicable time window; an object that has been deleted normally or has expired cannot be misjudged as lost by a node. If repair restores the same encrypted envelope, it should be verified against the original commitment, and a new proof record is appended in the Proof Registry for the original `object_id` / `version`; if re-encryption or format conversion occurs, the ciphertext digest may change, and the old and new envelopes, the digest mapping and the conversion receipt should be recorded, with the relationship saved under version rules, without silently replacing the original version. Replica repair, object version updates and evidence updates are different operations.
