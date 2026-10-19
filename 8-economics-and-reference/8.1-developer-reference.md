# 8 1 dev reference

## 8.1.1 API / SDK / Schema

Interfaces are divided into five groups by responsibility, and each access protocol maps onto the same command and event model:

| API group | Core operations                                                                                       | Required controls                                                                                                          | Main returns                                                              |
| --------- | ----------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| Identity  | Register / resolve Agent DIDs, Owner binding, identity continuity registration                        | Proof of principal control, nonce, replay prevention, old and new identity signatures or an independent recovery statement | DID Document, binding record, execution instance and continuity reference |
| State     | Create, read, update, list and export State Objects                                                   | Delegation, namespace, version precondition, idempotency key, purpose policy                                               | Object, version, commitment, receipt                                      |
| Handoff   | Generate / import Memory Packs, record receipt results                                                | Recipient key, purpose and term, manifest integrity, dependency and conflict policy                                        | manifest, handoff ID, sending and receiving receipts                      |
| Proof     | Query receipts, Trace digests, storage proofs and Attestations                                        | Evidence scope, privacy trimming, issuer and verification policy                                                           | Evidence references, verification results, failure reasons                |
| Admin     | Configure policy, revoke delegations, rotate content encryption keys, manage quotas and audit exports | Strong authentication, separation of duties, signed management policy, scope and budget constraints                        | Change records, data key versions, audit events, recovery points          |

The Agent DID binds the identity signing public key and the execution instance. Replacing the identity signing key creates a new `instance_id` and a new DID; changes to model, framework, name and version metadata, and rotation of the content encryption key, do not change the DID. `instance_id` identifies the execution instance, and `run_id` identifies one task run. In normal continuation, the old and new instances sign the continuity record and the Owner's delegation policy is validated; when the old identity is unavailable, a preset recovery authorization signs an independent recovery statement. Historical credentials remain bound to the old DID, and the new DID obtains permissions through a new delegation signed by the Owner.

A State Object uses the structure below. MEMO's own fields use snake\_case; native fields of external specifications, HTTP headers and ABI names keep their original spelling, and the adaptation layer performs explicit mapping.

```json
{
  "schema_version": "memo.state_object/1",
  "object_id": "mem_project_a_001",
  "type": "memory",
  "owner": "did:memo:user:example",
  "namespace": "project/a",
  "version": "v_001",
  "parent_refs": [],
  "provenance": {
    "source_type": "user_input",
    "source_refs": ["source://project/a/brief"],
    "producer_agent_did": "did:memo:agent:example",
    "producer_instance_id": "instance_example",
    "producer_run_id": "run_20261016_001",
    "confirmation_status": "confirmed",
    "confirmation_method": "policy_validation",
    "confirmed_by": "did:memo:service:policy-engine",
    "confirmation_policy_ref": "policy_project_a_memory_admission_v1"
  },
  "policy_ref": "policy_project_a_v1",
  "content_ref": "encrypted://content/mem_project_a_001/v_001",
  "commitment": { "algorithm": "sha256", "scope": "ciphertext", "value": "<digest>" },
  "created_at": "2026-10-16T02:00:00Z",
  "expires_at": null,
  "proof_refs": []
}
```

`object_id` is a stable logical identifier; `version` is an opaque, immutable and unique string assigned by the server. Clients compare version relationships using returned values and parent version references, and do not increment strings such as `v_001` or sort them lexically. `schema_version` identifies the structure version, and `parent_refs` records only the `object_id` / `version` of direct parent versions. Original sources, cross-object dependencies and transformation relationships are recorded in `provenance`. `type` is constrained by the schema registry to object types such as `memory`, `checkpoint`, `trace` and `artifact`.

The memory admission engine automatically validates source, signature, purpose, field schema and conflict policy. `confirmation_status=confirmed` means the object passed the admission policy specified by `confirmation_policy_ref`; `confirmation_method=policy_validation` and `confirmed_by` identify the processing method and the executing principal. This status does not replace the verification of source authenticity and business facts, and candidates rejected or quarantined by policy retain the reason and the source references.

`commitment.algorithm`, `scope` and `value` together define the digest. `scope=ciphertext` binds the full encrypted envelope bytes actually stored; the envelope uses the serialization and algorithm configuration specified by the schema registry, and the digest computation covers the byte range declared by the envelope. `content_ref` is a stable content reference connected to the physical backend through an independent location mapping; copying ciphertext as-is only updates the location mapping and retains the original version and digest. Re-encryption or format conversion creates a new object version and records the new envelope digest, the old-to-new mapping and the conversion receipt. The verifier performs verification according to the object's algorithm and envelope configuration, and rejects unknown configurations outright.

`proof_refs` is fixed as the evidence snapshot known at object creation. Evidence added asynchronously is appended to the Proof Registry indexed by `(object_id, version)`, recording the evidence type, issuer, time, status and verification reference. Query responses return the original object envelope and the supplementary evidence separately, and the aggregation layer merges and displays the object snapshot and the Registry records; the `proof_refs` inside the object and existing immutable versions stay as they are.

A write command submits the business payload, namespace, policy reference and version precondition; on creation the server assigns or validates a unique `object_id`, on update it keeps the existing `object_id` and generates a new `version`, time, policy admission result and receipt association. Client provenance statements are written after verification, and the admission status is generated by the policy engine. The plaintext `content` expresses the client-side pre-encryption payload, and the persistence path stores the encrypted envelope and `content_ref`. Gateway handles plaintext only when the controlled computation configuration explicitly grants decryption capability. Authorization operations are unified as `read`, `write`, `search`, `export`, `import`, `delete`, `train`, `share` and `execute_external`; the `create_checkpoint` tool maps to `write` and uses the Checkpoint's independent resource and purpose policy.

Write interfaces bind one business action with an `Idempotency-Key`, and update interfaces check a precondition version or ETag. Delegation registration is deduplicated by `issuer` and `nonce`; the same delegation can be used multiple times within its validity period and authorized scope, and per-request replay prevention and business idempotency are validated separately. Conflict handling uses deterministic rules: independent fields are merged, appended records are deduplicated by stable key, and other conflicts retain the branch and quarantine or reject the commit, avoiding silent overwriting. Lists use opaque cursors; times use a format with a time zone; large objects use chunked upload and restricted upload credentials. Error responses include a stable error code, the error stage, retryability and correlation identifiers, and avoid leaking whether a resource exists to callers without permission.

A request carries, through `Memo-Request-Proof`, the signature envelope defined in [4.1.2](file:///5907924/04-developer-guide/4-1-quickstart.md#c4-1-2), pinning the principal, instance, audience, network domain, method, target, business payload digest, `operation_id`, time window and an independent `request_nonce`. The identity challenge, delegation registration nonce, per-request nonce, contract call nonce and external payment authorization nonce are validated separately. A retry updates the per-request fields and retains the business operation identifier; the idempotency fingerprint excludes request identifiers, times, signatures and tokens. The business ledger atomically places a placeholder by Owner, namespace and business operation domain, and a same-key different-fingerprint request is rejected; status queries and result returns for the same operation continue to check the current authorization and revocation status.

`request_id` identifies a single request, `operation_id` identifies the same business action across retries and is associated with the `Idempotency-Key`, `receipt_id` identifies a receipt, and `run_id` identifies a task run. Returned statuses are recorded separately as follows:

| Field             | Values                                                                                                      | Meaning and handling rules                                                                                                                          |
| ----------------- | ----------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `decision`        | `allow` / `deny`                                                                                            | Policy decision, independent of the business execution result                                                                                       |
| `business_status` | `not_executed` / `succeeded` / `failed` / `unknown`                                                         | Business status; for `unknown`, query the same `operation_id` first and reconcile                                                                   |
| `receipt_status`  | `available` / `pending` / `failed`                                                                          | Retrieval and processing status of local receipt material; a failure goes into the recovery queue                                                   |
| `proof_status`    | `not_requested` / `pending` / `available` / `failed`                                                        | Background proof processing status; `available` means only that the material can be obtained, and the verification result is recorded independently |
| `payment_status`  | `not_required` / `required` / `verified` / `settled` / `failed` / `unknown` / `refund_pending` / `refunded` | MEMO's internal payment status mapping, independent of external protocol fields and the business result                                             |

Business write, local receipt persistence and asynchronous batch anchoring each commit their own status. When `business_status=succeeded` and `proof_status=pending`, the proof task is queried and the write is not repeated. Receipt recovery cross-checks the business commit log and existing `receipt_id` by `operation_id`, preventing a back-filled record from triggering duplicate side effects. When the payment result is `unknown`, the order is queried and reconciled first; a refund task uses the original payment reference and an independent idempotency key.

The SDK is responsible for signing, serialization, retry, pagination, version negotiation and error types, while authorization decisions are the responsibility of Gateway and the policy enforcement points. The schema registry records types, versions, serialization configuration and compatibility rules: adding optional fields stays backward compatible; deleting fields, changing meaning or tightening an enumeration constitutes a major version change; extension fields go into a registered extension area and are preserved as they are; unknown security fields and required capabilities are rejected.

## 8.1.2 Contract Methods and Events

On-chain contracts keep the minimum state needed for public verification, settlement or governance. The Contract Descriptor binds a contract domain to the chain ID, address, code hash, deployment transaction, ABI, source code reference and upgrade policy; the loader builds the call mapping after validating the descriptor signature, the chain identifier, and the consistency of code and ABI.

| Contract domain          | Method responsibility                                                                        | Event responsibility                                               | Mapping to the Agent State Layer                                                     |
| ------------------------ | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------ |
| Identity / registration  | Register identities, update metadata, record revocations and identity continuity commitments | Registration, metadata change, revocation, continuity registration | Agent DID resolution and revocation anchors; old and new DIDs retained independently |
| Delegation / Attestation | Issue, verify, revoke and register statement commitments                                     | Statement issuance, statement revocation                           | Owner binding, access delegation, TEE or service statements                          |
| Storage / challenge      | Submit commitments, initiate challenges, submit proofs, register repairs                     | Order creation, challenge, proof acceptance, repair result         | MEFS / Meeda storage responsibility and availability verification                    |
| Settlement / staking     | Deposit, withdraw, settle, slash                                                             | Fund deposit, settlement, reward, slashing                         | Fees for storage, verification, computation and services                             |
| Data assets              | Create assets, grant purpose licenses, transfer, revoke authorization                        | Asset registration, permission change, transfer record             | Data or Agent metadata assets selected by Owner policy                               |

A method description contains the calling permission, parameters, return values, events, state changes, fees, failure conditions, replay prevention, reentrancy control, pause behavior and upgrade policy. An event description contains the indexed fields, a unique identifier, finality, chain reorganization handling and the off-chain object references. The signing domain binds the network, contract, method, parameter digest, validity period and nonce, preventing replay across chains, contracts and purposes.

An on-chain observation serves as an independent evidence item of a signed business receipt, using the structure below to associate the business operation with chain events. The parameters in angle brackets are resolved from the operation context, the Deployment Registry and the chain indexer, and are written to the record after validation passes.

```json
{
  "schema_version": "memo.chain_observation/1",
  "operation_id": "<idempotent-operation-id>",
  "request_id": "<request-attempt-id>",
  "receipt_id": "<receipt-id>",
  "network": "<network-id>",
  "chain_id": "<chain-id>",
  "contract": "<verified-address>",
  "transaction_hash": "<hash>",
  "block_ref": "<block-number-or-hash>",
  "finality_status": "<observed-finality>",
  "event_ref": "<contract-event-reference>",
  "state_ref": { "object_id": "mem_project_a_001", "version": "v_001" },
  "observed_at": "2026-10-16T02:00:00Z"
}
```

The chain indexer updates the observation status according to the chain configuration; a chain reorganization triggers re-verification and withdraws the finality mark of affected observations. The indexer appends re-verification records and associated receipt verification results, without rewriting signed statements and without redoing off-chain business. Before replay execution, the `operation_id` and the actual side effects are cross-checked first. Signatures, event authenticity, log coverage, storage claims and business results are verified separately.

The descriptor of an upgradable contract records the proxy, implementation, administrator roles, time lock and upgrade events. Permission changes and upgrades take effect through signed policy validation, simulated execution, a time lock and a post-execution code hash check, and the deployment registry and historical records save the before-and-after mapping in sync.

## 8.1.3 Networks, Contract Addresses and Environments

The Deployment Registry is the configuration entry point for networks, contracts and service endpoints. The build and deployment process assembles component manifests, network configuration and deployment transaction outputs into the registry, signed with a release key. Callers load configuration from a trusted registry reference, pin the environment and version, and perform automated validation.

```yaml
schema_version: memo.deployment_registry/1
release: <memo-release>
environment: <local-or-devnet-or-testnet-or-mainnet>
network:
  name: <network-name>
  chain_id: <chain-id>
  rpc:
    - <official-rpc-url>
contracts:
  - name: <contract-name>
    proxy: <optional-address>
    implementation: <verified-address>
    deployment_tx: <transaction-hash>
    abi: <versioned-artifact-reference>
    source_verification: <explorer-or-repository-reference>
services:
  gateway: <base-url>
  proof: <base-url>
  facilitator: <base-url>
published_at: <timestamp>
signature: <release-signature>
```

Items in angle brackets are configuration parameters: `release` and the ABI references come from the versioned build manifest; the network and RPC come from the description of the selected network; the address, deployment transaction and code verification reference come from the deployment output; service URLs come from trusted service discovery records; and the time and signature are produced by the release job. A configuration that uses only local state can omit the chain module and the Facilitator; an enabled module must have complete dependencies. The loader rejects unresolved parameters, expired signatures, environment conflicts and URLs not in the endpoint allowlist.

| Environment                     | Purpose                                                | Data and funds                            | Operational controls                                                                                         |
| ------------------------------- | ------------------------------------------------------ | ----------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Local (development environment) | Offline development, unit validation, fault simulation | Test data and local test keys             | Rebuildable isolated environment; configured separately from the Local Vault deployment form used day to day |
| Devnet                          | Team integration, protocol and migration validation    | Test assets without value                 | A network reset produces a version event, and clients refresh the registry and clean up invalid references   |
| Testnet                         | Interoperability, load and recovery tests              | Test assets, synthetic or redacted data   | Pinned test versions, with migration windows and failure scenarios written into the test configuration       |
| Mainnet / Production            | Business settlement and long-term state                | Business data, keys and settlement assets | Least privilege, key isolation, change control, recovery points and service level policy                     |

Initialization verifies in turn the release signature, schema, environment name, `chain_id`, address format, code hash, ABI digest, endpoint certificate and service health; if any required check fails, the related module stops accepting integrations and returns an explicit error. Clients cache endpoints by registry version and revalidate after receiving a signed update, to avoid referencing old addresses across environments.
