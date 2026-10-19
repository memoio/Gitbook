# 9 1 capability profiles

## 9.1.1 Basic Profile: Controllable Memory

> **Profile positioning: Vault, Gateway and the policy engine organize independent memory storage, automated admission, retrieval, versioning and import / export, while keys and authorization are controlled by the Owner.**

The Basic profile imports sessions, task records, files and Traces into the state layer, retains their sources and extracts candidate memories. The policy engine runs automated admission based on signatures, sources, purposes, field patterns, privacy and conflict rules; candidates that meet the rules enter long-term memory, and others are rejected or quarantined with their reasons retained.

Components and interfaces include:

* The Vault State Object interface, with the storage backend selected as Local, Hosted or MEFS by configuration;
* Importers for sessions, tasks, basic Traces and memory, which keep the original source references;
* The Candidate Memory admission policy, recording `candidate`, `confirmed`, `rejected` and `quarantined` separately;
* Version and provenance schemas for Memory, State Object and Artifact, where `provenance.source_refs` records sources and `parent_refs` records direct parent versions;
* User key or organizational KMS policy, configuring content encryption, backup, data key rotation and recovery;
* Memory Pack manifests, encrypted envelopes and import / export interfaces;
* Unified operation and error semantics across REST, MCP, Gateway and the SDK;
* Locally signed receipts and a recovery queue for access, modification, export and deletion.

_Figure 32: Automated Admission of Candidate Memory and State-Saving Flow_

Automated validation uses the same sample with provenance to complete import, admission, retrieval, restart recovery, export and re-import at another site. The validator checks that out-of-scope requests are rejected, that a candidate never silently overwrites an existing version, that indexes and caches are handled under the deletion policy, and that after a power loss the business commit, receipt persistence and proof tasks can each be queried and recovered separately.

`confirmed` means the candidate passed the specified admission policy; `confirmation_method=policy_validation` records the processing method, and `confirmed_by` identifies the policy engine. Source authenticity and business facts use independent verification rules. When the rules are not satisfied, the candidate and the error reason are retained, and automated admission is not interpreted as the fact automatically holding.

Migration verification binds the algorithm and the digest scope. `content_ref` uses a stable reference with an independent location mapping; migrating the same ciphertext only updates the location mapping, and the original version and digest stay unchanged. Re-encryption or format conversion creates a new object version and records the old and new envelopes, the digest mapping and the conversion receipt. Export is an independent event, and the source object can remain `active`; authorization status, deletion status and key rotation status are managed separately. A background `proof_status=pending` only triggers a proof task query and does not repeat a business write that has already succeeded.

## 9.1.2 Collaboration Profile: Portable State, Traces and Proofs

> **Profile positioning: On top of the unified object and authorization mechanism, it connects execution identity, versioned business state, cross-principal handoff, run traces and independently verifiable evidence.**

Checkpoint, task context, Artifact, tool result summaries and Trace keep independent version relationships. `parent_refs` records direct parent versions, and `provenance` records sources and cross-object dependencies. A Memory Pack encapsulates the corresponding objects and references by recipient, purpose and term, and the receiving side runs permission, integrity, dependency and conflict checks before restoring the agreed business state.

Components and interfaces include:

* User identity separated from Agent DID, with registration, binding, delegation, revocation and identity continuity recorded separately;
* Gateway policy objects constraining resources, operations, purposes, time, tools, fees and external side effects;
* Versioned schemas for Checkpoint, Version, Trace, Artifact and Handoff;
* Memory Pack manifests, recipient key encapsulation, partial import, deterministic conflict handling and two-way receipts;
* Manifest verification, envelope mapping and migration receipts among the Local, Hosted and MEFS backends;
* Unified association of access, provenance, runtime, storage commitments, challenges and proof references;
* Consistent handling of fault recovery, idempotent retries, chain reorganization, expiry and revocation;
* Schema converters, Compatibility Profiles and an automated validation suite.

The Agent DID binds the identity signing public key and the execution instance, and a logical Agent can persist across models. Replacing the identity signing key creates a new instance and a new DID; changes to model, framework, name, version metadata and content encryption key do not change the DID. In normal continuation, the old and new instances sign a continuity record that is validated by the Owner delegation policy; when the old instance cannot sign, a preset recovery authorization signs an independent recovery statement. Historical receipts, reputation evidence and run records remain bound to the original DID, queries present them combined through continuity references, and the Owner issues a new delegation for the new DID.

Cross-Agent recovery uses a replayable business scenario: Agent A saves a Checkpoint, Agent B obtains the objects allowed for handoff through an independent authorization, restores the state within the model, framework, tools and dependencies specified by the Compatibility Profile, and continues to generate an Artifact. Recovery checks cover task progress, input references, dependency versions, idempotency records of external actions and recovery conditions; different models continue the task from the same business state, and the results they generate do not have to be identical.

The evidence verifier checks delegations, versions, provenance, sending and receiving receipts and outcome rules separately. A signature proves that the issuer made a statement, and a Merkle inclusion proof shows that a record belongs to an anchored batch; neither alone proves that an event is authentic or that a log is complete. An Artifact is judged for conformance by the task's deterministic acceptance policy or a business verifier, and the verification result records the rule version, inputs and evidence references.

Security validation covers prompt injection, malicious Memory Packs, forged provenance, signature replay, key leakage, policy races, cost overruns and Provider outages. Revocation propagation is tracked by the effective record and policy period at each enforcement point, and the revocation status is rechecked before delivery. Backend conversion verifies the old and new envelopes, digest mapping, object identifiers, parent version graph, provenance and permissions. Proofs are verified by type, version, issuer and time window, and storage claims and content authenticity are handled separately.

## 9.1.3 Ecosystem Profile: Framework Adaptation and Selective Delivery

> **Profile positioning: Versioned adapters connect Agent Hosts, orchestration frameworks, memory engines, enterprise systems and data markets, completing collaboration and authorized delivery under a unified permission and evidence mechanism.**

The Ecosystem profile maps external protocols and object formats onto MEMO's state, delegation, receipt and payment contracts. Service discovery first matches the protocol version, schema, transport, authentication and required capabilities, and then loads the corresponding adapters and validation rules.

Components and interfaces include:

* Memory, checkpoint, artifact and tool adapters for Agent frameworks;
* Versioned mappings and interoperability tests for MCP, A2A, Agent Skills and x402;
* Connectors for enterprise IdPs, wallets, KMS, audit, data governance and policy engines;
* Attestation Servers, TEE / controlled computation and multi-issuer trust policies;
* Selective authorized delivery of state packages, knowledge, experience and results through the Data Market;
* Isolated adaptation, rights mapping and validation rules for assetization interfaces such as ERC-7857 / ERC-7829;
* Service discovery, quotes, settlement, disputes, licenses and usage evidence;
* Monitoring and automated threshold control of privacy, security, economics and operating quality.

The asset flow handles only objects that the Owner selected in a signed policy and whose rights scope meets the conditions. A scanner checks source licenses, third-party content, sensitive fields, keys and private data; content that does not satisfy the delivery rules is excluded from the manifest or redacted according to the rules. Asset ownership, read rights, training rights, redistribution rights, commercial use and service usage rights are expressed separately, and the permissions a recipient obtains are determined by the corresponding delegation and license.

Adaptation validation records recovery success rate, state loss rate, authorization error rate, Proof verification rate, revocation latency at each enforcement point and operational explainability. Each metric is bound to its samples, calculation method, failure categories, configuration and test time; thresholds are loaded from the service-level policy. For out-of-range metrics, the monitor applies rate limiting, isolation, load shedding or rollback; the number of adapters is only an asset inventory statistic and does not replace behavioral validation.
