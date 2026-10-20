# Appendix B: Technical Specifications and Configuration Contracts



Deployment configuration combines identity, objects, permissions, evidence, payment and the runtime environment into a verifiable service contract. A configuration is signed by a trusted issuer and declares its scope, version, validity period and content digest; component startup and configuration switchover are both validated by the loader for signature, dependencies, interfaces and environment constraints, and are loaded or rejected according to the result.

### B.1 Core Technical Specifications and Configuration Relationships

| Technical domain                   | Unified approach                                                                                                                                                                                                               | Configuration and automated validation                                                                                                                                                                                                          | Related sections     |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------- |
| Agent DID and instance             | The DID binds an identity public key and an execution instance; replacing the identity key forms a new instance and a new DID, the old and new identities are linked through a continuity record, and delegations are reissued | The identity resolution configuration pins the signature suite, encoding and resolution rules; verifies instance control proof, Owner binding and continuity signatures; a recovery statement uses an independent preset recovery authorization | 2.3, 5.1, 8.1, 9.2.1 |
| State Object                       | A stable `object_id` is separated from an immutable `version`; the server assigns versions, `parent_refs` records direct parent versions, and the structure is identified by `schema_version`                                  | Validates the schema, parent versions, provenance, encrypted envelope and commitment; writes use a version precondition and an idempotency key; conflicts are merged, appended or quarantined per the namespace policy                          | 3.2, 4.1, 8.1        |
| Memory admission                   | Candidate content enters the reusable memory set by source, signature, purpose, field structure and conflict rules                                                                                                             | The policy engine outputs `confirmation_status`, `confirmation_method`, `confirmed_by` and `confirmation_policy_ref`, recording the admission evidence                                                                                          | 3.2, 4.2, 5.2        |
| API, SDK and adapters              | The API, schemas, SDK and protocol adapters jointly reference the compatibility configuration, and external fields connect to the MEMO data model through mappings                                                             | Installation and startup resolve artifacts, digests, dependencies and entry points from the component manifest; the handshake validates version, transport, authentication, action set and schema                                               | Chapter 4, 7.3, 8.1  |
| Receipts, Proof and recovery       | Gateway persists signed receipts, Proof is aggregated and anchored asynchronously, `proof_refs` keeps the snapshot at object creation, and later evidence is appended to the Proof Registry                                    | Configures issuers, aggregation intervals, anchoring policy, and query and retry limits; validates duplicates, gaps, signatures and proof scope by operation and object version                                                                 | 2.4, 3.1, 5.2, 7.2   |
| Deployment modes and keys          | Local, Hosted and MEFS use a unified object interface; the deployment configuration separately defines the key holder, decryption location, administrative permissions, backup and revocation scope                            | Checks key references, service identity, environment isolation, storage policy and recovery conditions; migrating as-is updates the location mapping, and re-encryption generates a new object version and conversion receipt                   | 2.2, 3.3, 4.3, 5.3   |
| x402 and Facilitator               | Executed in order: eligibility pre-check, 402 quote, payment authorization, verification, settlement, resource re-authorization and delivery; delivery failures are compensated by rule                                        | The payment configuration pins scheme, network, asset, payee, quote validity period, budget and idempotency key; unknown results are queried and reconciled, and refunds use an independent idempotent operation                                | 4.2.3, 7.1.3, 8.2.1  |
| Standards and adaptation           | Each adapter pins its specification reference, interface version, feature set, transport, authentication and field mappings                                                                                                    | Both sides' capability manifests are compared at startup, and contract checks cover serialization, authentication, errors, retries and version boundaries                                                                                       | 4.2, 7.3, 8.2.2      |
| Networks and contracts             | The Deployment Registry associates environment, chain ID, RPC, contract addresses, ABI digests, proxies and upgrade permissions                                                                                                | Validates network identifiers, address formats, on-chain code, ABI and permission configuration; read and write entry points use the matching environment and contract configuration                                                            | 6.2, 8.1.2–8.1.3     |
| Metering and settlement parameters | The PriceBook is versioned by product, unit, environment and effective time; budgets, quotas, node incentives and challenge rules are modeled separately                                                                       | A quote binds the metering unit and policy digest; bills are deduplicated by business operation, and compensation, refunds and incentives are booked from verifiable events                                                                     | 8.2, 8.3             |
| Service discovery and support      | Documentation, SDK, API, status, support, security feedback and community endpoints are described uniformly by the endpoint registry                                                                                           | Verifies signatures and validates domain, endpoint purpose, access method, validity period and health status; selects a valid endpoint by policy or returns a discovery error                                                                   | 1.3, 8.1, 9.2–9.3    |

Identity, configuration signing, data encryption, payment and audit use mutually distinct key purposes and permission scopes. Components parse only configuration that has been validated, and do not interpret endpoint reachability, payment success or the existence of evidence as resource authorization or business acceptance.

### B.2 Configuration Versions, Automated Validation and Rollback

The configuration manifest includes `registry_version`, `environment`, `component_versions`, `api_versions`, `schema_versions`, `adapter_profiles`, `policy_refs`, `network_refs`, `key_refs`, `config_digest`, `valid_from`, `expires_at`, `issuer` and `signature`. Every configuration change generates a new immutable manifest, and components record the manifest digest they use, so that requests, metering, receipts and proofs can be traced back to the same runtime configuration.

The loader runs in the following order:

{% stepper %}
{% step %}
### Validate origin and content

Read the signed manifest from a trusted endpoint, and verify the issuer's eligibility, digest, signature, scope and validity period.
{% endstep %}

{% step %}
### Resolve dependencies

Resolve references to the API, schemas, SDK, adapters, policies and contracts to pinned versions, and check reference closure, types, allowed ranges and environment consistency.
{% endstep %}

{% step %}
### Verify the environment

Validate the service identity, chain ID, the correspondence between contract code and ABI, key purposes, decryption location, backend capabilities and quota configuration.
{% endstep %}

{% step %}
### Run contract checks

Per the validation configuration, check object round trips, authorization denial, revocation, idempotency, payment reconciliation, evidence queries and recovery paths.
{% endstep %}

{% step %}
### Switch configuration

Perform an atomic switch to the valid manifest; a request binds the configuration digest at the start of processing, and security-relevant revocation and policy changes are rechecked across steps.
{% endstep %}

{% step %}
### Handle anomalies

A failed manifest stays quarantined; rollback selects only configurations that are still within their validity period and meet security constraints. When no qualified configuration exists, the corresponding requests are rejected, and errors, events and existing object versions are preserved.
{% endstep %}
{% endstepper %}

Compatibility changes are handled through version windows and converters. Changes involving DID, authorization, key, object, payment or proof semantics each define input compatibility conditions, conversion mappings, acceptance rules, rollback points and handling of external side effects; historical signatures and object versions keep their original meaning. Runtime metrics are organized by environment, load, measurement method, time range and configuration digest, and metric targets and actual observed values are recorded separately.
