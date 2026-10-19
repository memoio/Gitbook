# 9 2 support maintenance

## 9.2.1 FAQ

<details>

<summary>Is the MEMO Agent State Layer a new chain or a new storage network?</summary>

The MEMO Agent State Layer is state infrastructure built on the MEMO Protocol that connects MEFS, Meeda, identity, verification and execution capabilities. Vault manages state objects, Gateway manages managed access, and Proof organizes evidence; networks and contracts are bound through the Deployment Registry, and the state layer uses these underlying capabilities through protocol interfaces.

</details>

<details>

<summary>What are the responsibilities of MEMO Protocol, MEFS, Data Wallet, Data Market and Meeda?</summary>

Protocol provides the shared protocol and network rules, MEFS provides decentralized storage, Data Wallet manages user identity and key control, Data Market handles selective authorized delivery, and Meeda provides data availability evidence. Each product keeps its own interface and protocol boundary, and they are combined through adapters and versioned service manifests.

</details>

<details>

<summary>Why are user identity and Agent identity separated?</summary>

The user is the controller of data and authorization, and the Agent is the executing principal that accepts a delegation. An independent delegation limits scope, operations, purpose and term, and the executing principal can be paused, replaced or revoked, with its changes not automatically transferring user ownership. `instance_id` identifies the execution instance, and `run_id` identifies one task run.

</details>

<details>

<summary>Can an Agent DID prove that an Agent belongs to a certain user?</summary>

A DID expresses the identifier of the executing principal, and a signature proves control of the key. Owner binding uses a binding statement signed by the user together with proof of Agent control, recording the validity period, revocation information and signing key identifier. The binding association is stored separately from the access delegation, and Gateway checks the delegation and policy independently on every request.

</details>

<details>

<summary>Does changing the model, execution instance or key change the Agent DID?</summary>

The Agent DID binds the identity signing public key and the execution instance. Replacing the identity signing key creates a new `instance_id` and a new DID; changes to model, framework, name and version metadata, and rotation of the content encryption key, do not change the DID. The old and new DIDs are linked through a signed continuity record, and when the old identity is unavailable, a preset recovery authorization signs an independent recovery statement. Historical credentials keep the original DID, and the new identity obtains permissions through a new delegation signed by the Owner.

</details>

<details>

<summary>What is the difference between Memory, State Object, Trace and Artifact?</summary>

Memory is a memory unit that later tasks can reuse; a State Object is a general versioned state container; a Trace records the steps, tools and results of one run; and an Artifact is a deliverable result. They are associated through object references, and each has its own lifecycle and permission configuration.

</details>

<details>

<summary>Does MEMO store an Agent's complete thought process?</summary>

A Trace stores observable input and output references, tool calls, decision summaries, results, errors and policy decisions, trimmed according to privacy rules. Internal implicit reasoning is not among the default collected fields; audits proceed from verifiable events, authorizations and result records.

</details>

<details>

<summary>Is all data put on-chain?</summary>

State plaintext, keys and high-frequency reads and writes stay off-chain, while the chain holds the necessary identity commitments, Audit Roots, proof results, settlement and governance records. Meeda keeps blobs off-chain and registers their commitments. Each object declares its disclosure scope and retention method in its schema and deployment policy.

</details>

<details>

<summary>Do all memories become NFTs or enter the Data Market?</summary>

Everyday state is private by default. Only objects selected by the Owner's signed policy that pass the checks on source licenses, purpose, sensitive data and recipient eligibility enter the assetization and delivery flow, with read, training, redistribution and ownership authorized separately.

</details>

<details>

<summary>Can a revoked permission take back plaintext the other party has already seen?</summary>

Once revocation takes effect at the policy enforcement points, it blocks subsequent managed requests and key issuance, and propagation is tracked through versions and effective records. Plaintext that has already been obtained, old ciphertext and the corresponding keys remain under the control of the receiving environment. Data minimization, short-lived authorization, controlled computation, key rotation and audit together constrain later access, but content already disclosed cannot be recalled remotely.

</details>

<details>

<summary>Can a storage proof prove that the content of a memory is correct?</summary>

A storage proof verifies the storage claim for a specified data commitment within the agreed window. Source signatures, references, runtime evidence and business rules separately address provenance, events and outcome conformance; passing one kind of proof does not automatically make other claims hold.

</details>

<details>

<summary>Will MCP, A2A and Agent Skills replace the MEMO API?</summary>

MCP connects tools and resources, A2A connects Agent collaboration, Agent Skills packages operating instructions, and REST / SDK provide direct integration. Adapters map each entry point onto the same state, authorization and receipt semantics, and the Compatibility Profile matches the standard version, adapter version, authentication method and required capabilities.

</details>

<details>

<summary>Once an x402 payment succeeds, can the state definitely be read?</summary>

Resource delivery requires both the payment conditions and the current access policy to pass. The flow is eligibility pre-check, 402 quote, the client retrying Gateway with the authorization, Facilitator verification and settlement, Gateway re-authorization, and execution or delivery. The Facilitator neither reads state plaintext nor authorizes resources. An operation that was settled but failed to deliver enters refund or reversal, and an unknown settlement is queried and reconciled first.

</details>

<details>

<summary>How do I migrate among Local, Hosted and MEFS?</summary>

The exporter generates a Memory Pack with versions, provenance, permissions and evidence references, and the receiving side checks the manifest, dependencies, recipient authorization and envelope. Migrating the same ciphertext updates the location mapping for the stable `content_ref` and retains the original version and digest; re-encryption or format conversion creates a new version and records the old and new envelopes, the digest mapping and the conversion receipt. Logical objects and the parent version graph are continuously retained, and a new identity still uses an independent delegation.

</details>

<details>

<summary>Once an object is marked deleted, does it disappear from all media immediately?</summary>

A deletion task handles active objects, indexes, caches, keys and backend replicas separately, and returns the processing stages and retention policy. Distributed replicas and backups are cleaned up according to configuration, and on-chain commitments are subject to the chain's retention rules. Deletion status and authorization revocation are recorded separately, to avoid misreading a stop in access as every medium being cleared at once.

</details>

<details>

<summary>How does a client choose which service capabilities to integrate?</summary>

A client loads the signed service manifest, reads the `feature_set`, the protocol and schema versions, the environment and the authentication method, matches the Compatibility Profile, and runs the necessary endpoint health checks. A session is established once dependencies are complete and permissions and configuration match; on an anomaly, a missing capability, version conflict or endpoint error is returned, and the connection is not degraded by bypassing authentication.

</details>

## 9.2.2 Troubleshooting, Support and Security Feedback

The diagnostics tool first identifies the failing layer and then applies the status and idempotency rules. Diagnostic packages use a field allowlist, secret and credential scanning, plaintext trimming and encrypted transport, to avoid sending private keys, full tokens, decrypted Memory Packs or user plaintext into tickets and public channels.

| Symptom                                    | Checks                                                                               | Action                                                                         | Diagnostic information that may be recorded            |
| ------------------------------------------ | ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------ | ------------------------------------------------------ |
| Identity resolution failure                | DID format, network, signing key, registration and binding records, resolution rules | Stop identity degradation and return a resolution error                        | `request_id`, DID digest, resolver version, error code |
| No permission / 403                        | Delegation scope, term, action, namespace and revocation status                      | Reject execution, without bypassing via an administrator key                   | `policy_ref`, decision summary, Agent DID, time        |
| Version conflict                           | `expected_parent_version`, concurrent updates, direct parent versions                | Merge by rule, deduplicate and append, or retain and quarantine a branch       | `object_id`, `version`, operation order                |
| Read or decryption failure                 | Content reference, data key version, envelope recipient                              | Isolate the failed read and check the envelope and authorization               | Commitment, backend, `key_id`, error stage             |
| Proof verification failure                 | Algorithm and scope, time window, issuer, chain finality                             | Save the failure reason, separately from business status                       | `proof_id`, verifier version, block reference          |
| Proof processing                           | `proof_status=pending`, background tasks and anchoring batch                         | Query or recover the proof task, without redoing a successful write            | `operation_id`, `receipt_id`, proof task identifier    |
| Write result unknown or receipt processing | State version, commit log, receipt persistence, idempotency record                   | Query, reconcile and recover by the original operation                         | `operation_id`, each `request_id`, `receipt_id`        |
| Payment duplicate / failure                | Idempotency key, asset, network, settlement and delivery status                      | Query the order, reconcile, recover or compensate, avoiding double charges     | `operation_id`, `payment_ref`, response code           |
| Incomplete Handoff                         | manifest, missing chunks, receiving receipt, permission term                         | Quarantine the imported package and resume transfer per the manifest           | `pack_id`, `manifest_hash`, missing object list        |
| Node out of sync                           | Network, clock, disk, version, peer status                                           | Sync per the recovery configuration, retaining the data directory and evidence | Node version, health summary, log time window          |

A standard diagnostic package records the software version, environment, network and `chain_id`, non-sensitive object and operation identifiers, error code, time window, reproduction steps, expected and actual results, recent configuration changes and redacted logs. `request_id`, `operation_id`, `receipt_id` and `run_id` correspond to the request, business action, receipt and task run respectively. Payment, business, receipt and Proof are queried separately, and `decision=allow` means only that the policy allowed the request.

Incident grading and response rules are as follows:

* **S0 / Critical**: private key or large-scale plaintext leakage, unauthorized irreversible external operations, or compromised consensus or settlement integrity; immediately isolate the affected scope, suspend the corresponding execution permissions and start the key, delegation and evidence handling procedures.
* **S1 / High**: cross-tenant access, revocation failure, systemic data unavailability or duplicate settlement; block the spread, preserve evidence and run the authorization, recovery and reconciliation procedures.
* **S2 / Medium**: a single-tenant outage, or a recoverable data or compatibility issue; dispatch recovery tasks per the service-level configuration and record progress, retries and rollback results.
* **S3 / Low**: documentation, experience and low-impact operating issues; handle through the maintenance queue and associate with the release record.

The private security feedback channel is resolved from the Official Links registry, recording the encrypted reporting method, recipient key, accepted scope and response policy. The receiving service automatically generates a report receipt and an incident identifier, the diagnostics tool trims irrelevant user data, and the response process coordinates isolation, remediation, verification and disclosure per configuration. Public records contain the affected scope, key and authorization handling, versions and self-check methods for users; sensitive exploitation details follow the disclosure policy.

## 9.2.3 Release Notes and Compatible Migration

Release notes connect user impact, interface contracts, compatibility and automated validation material. The build system generates the record from commits, schema diffs, test reports, the registry and migration jobs, then verifies all references and artifact digests before signing and archiving it. Machine-readable entries bind the version, date, configuration scope and change type.

```markdown
## <release_version> — <release_date>

- schema_version: <schema_set_version>
- feature_set: <set of interface and operation capabilities>
- compatibility: <combinations of protocol, client, framework, backend and environment>
- validation_refs: <automated validation reports and artifact digests>
- registry_ref: <signed deployment registry>

### Added
- <interface, field or capability and its scope>

### Changed
- <behavior change, affected objects and configuration>

### Fixed
- <error condition, fixed behavior and regression validation reference>

### Security
- <risk scope, control rules, handling and validation reference>

### Deprecated
- <interface>: compatible until <date>; replacement <replacement>; converter <converter_ref>

### Removed
- <removed interface, last compatible version and migration entry point>

### Migration
1. <encrypted backup and recovery point>
2. <dependency and compatibility checks>
3. <idempotent migration job>
4. <integrity, authorization and business verification>
5. <failure rollback policy>

### Operational boundaries
- <applicable conditions, limits and operating rules>
```

Deprecation configuration is synchronized to documentation, SDKs, service responses and machine-readable announcements, and clients automatically detect an interface's compatibility cutoff and replacement mapping. Migrations of identity, keys, encryption, schemas or contracts run a dry run, a recovery point, the formal conversion, integrity verification and failure rollback; replacement of the identity signing key and rotation of the content encryption key are handled separately. Historical documents retain their applicable versions, replacement entry points and compatibility scope, and are isolated from the current deployment entry points.
