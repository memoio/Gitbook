# 2 1 protocol

## 2.1.1 Protocol Positioning, Network Roles and Capability Boundaries

> **Product positioning: a unified protocol and network foundation connecting storage, identity, verification, computation, execution and settlement, providing node collaboration, verifiable retention and settlement rules for agent state services.**

MEMO Protocol is the protocol foundation that connects storage, identity, verification, computation, execution and settlement. It defines the collaboration among users, providers, guardians and other network nodes, and gives the upper-layer products reusable network capabilities. Guardians take part in challenges and, according to the challenge results and network rules, trigger payments, tying provider income to verifiable service results. Role permissions, challenge periods, settlement conditions and service scope are written into versioned network configuration; before accepting orders, challenging and settling, a node validates the configuration signature, the network identifier and the validity period.

| Role                               | Core responsibility                                               | Continuation in the Agent State Layer                                  |
| ---------------------------------- | ----------------------------------------------------------------- | ---------------------------------------------------------------------- |
| User                               | Submits data, pays storage or service fees, holds keys            | Becomes the state owner and the originator of delegations              |
| Provider                           | Provides storage space, stores data, responds to challenges       | Stores the encrypted state objects that Vault places in MEFS           |
| Guardian                           | Order management, challenges, credentials and repair coordination | Continues to support storage proofs and network governance             |
| Verification / contract components | Registration, challenges, settlement and adjudication             | Support Proof's anchoring, verification and necessary dispute handling |

Protocol does not replace specific models or agent frameworks, does not directly define business processes, and does not require all runtime content to be placed on-chain. Upper-layer products use storage, identity, proof and settlement capabilities through Protocol.

## 2.1.2 Mapping Between the Six Protocol Layers and the Agent State Layer

The six layers of MEMO Protocol describe network capability domains; the Agent State Layer organizes the responsibilities for objects, identity, authorization, storage, evidence and controlled processing around a single state request. The two are connected through versioned interface mappings: inputs and outputs use unified object references, identity and purpose checks propagate along the request chain, and verification results are returned for defined claims.

| MEMO Protocol layer | Protocol responsibility                | How the Agent State Layer carries it                                                      |
| ------------------- | -------------------------------------- | ----------------------------------------------------------------------------------------- |
| Data storage layer  | Data retention, redundancy, repair     | Vault storage backends, MEFS, persistent storage of state objects                         |
| Computation layer   | TEE, compute and data processing       | Authorized compute platform, TEE, retrieval, redaction and controlled processing          |
| Verification layer  | Proofs, challenges and verification    | Storage proofs, audit roots, source statements and runtime evidence                       |
| Execution layer     | Contracts, registration and settlement | On-chain anchoring, state registration, challenges, settlement and necessary adjudication |
| Identity layer      | User / File / Device DID               | User DID, Data DID, Device DID, Agent DID and the delegation chain                        |
| Intelligence layer  | Access, services and composition       | Gateway policy enforcement, MCP / SDK access and state services                           |

The Protocol six layers explain network capability domains, while the Agent State Layer six layers explain collaboration responsibilities within a state request. The mapping table preserves the different levels of abstraction of the two, making independent configuration and verification possible.

## 2.1.3 Relationship Between Protocol and Vault / Gateway / Proof

One protocol foundation and three Agent products answer the following questions respectively:

| Layer              | Question answered                                                        | Primary responsibility                                           | Typical output                                      |
| ------------------ | ------------------------------------------------------------------------ | ---------------------------------------------------------------- | --------------------------------------------------- |
| MEMO Protocol      | How underlying capabilities are provided and verified by the network     | Protocol, nodes, storage, verification, execution and settlement | Network services, proof and settlement capabilities |
| MEMO Vault         | What the state is, where it lives, and how it is versioned and encrypted | Objects, namespaces, versions, keys and backends                 | State Object, Version, Memory Pack                  |
| MEMO Agent Gateway | Who can do what under which delegation                                   | Identity, policy, revocation, access and receipts                | allow / deny, Access Receipt                        |
| MEMO Proof         | How an access, provenance or storage fact is verified                    | Audit roots, commitments, proofs and verification interfaces     | Audit Root, Proof Pointer                           |

The typical call relationship is: an Agent obtains authorization through Gateway, Gateway reads and writes Vault, and Vault can use MEFS; facts produced by Gateway and Vault are handed to Proof for aggregation, and the underlying network and contracts are supported by Protocol.
