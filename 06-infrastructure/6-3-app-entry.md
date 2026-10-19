# 6 3 app entry

## 6.3.1 Data Wallet and the Identity Control Entry Point

Data Wallet is the control entry point for user identity, keys, data assets and Agent state, expressing permissions through access roles such as Reader and through limited delegations. The console turns user operations into signed policies or authorization requests and provides the following functions:

* View the memory, tasks, results and provenance in Vault.
* View the logical Agent, the Agent DID, the execution instance `instance_id` and the task run `run_id`.
* Issue, view and revoke delegations.
* Manage Vault content encryption keys, rotation and custody mode, with the replacement flow for Agent identity signing keys explained separately.
* Query access, runtime and proof records.
* Export Memory Packs, and initiate deletion and derived-item handling.
* Separately control memory use, training, publishing and resharing.

The Agent identity scheme determines the Agent DID by "Agent identity signing key + execution instance". The logical Agent represents a service or role that persists across models, `instance_id` identifies the execution instance, and `run_id` identifies one task run. A change of model, framework, name or version does not by itself create a DID; replacing the identity signing key creates a new instance and a new DID.

Data Wallet stores the continuity record that is dual-signed by the old and new identities and validated by Owner policy; when the old identity is unavailable, a preset recovery authorization is used to issue an independent recovery statement. Historical records and reputation remain bound to their original DID and are shown through associated queries. A controlled recovery of the same instance with the original key keeps the DID; a new concurrent instance uses an independent identity key and DID. User binding, access delegation and Agent registration are managed separately, and the new DID obtains permissions through a new delegation signed by the Owner. Rotating the Vault content encryption key does not change the Agent DID.

Data Wallet interacts with the chain, Vault, Gateway and storage backends as the control entry point and does not need to hold the plaintext of all personal data. The status shown in the console must come from the actual policy and audit services, and UI status cannot substitute for backend authorization.

{% hint style="info" %}
Once revocation is confirmed effective at the relevant policy enforcement points, subsequent controlled requests are blocked; the console shows the effectiveness receipts, propagation time and abnormal states of each enforcement point; plaintext, old ciphertext and corresponding keys already obtained are not recalled automatically.
{% endhint %}

## 6.3.2 Data Market and Selective State Assetization

Data Market stores encrypted content on MEFS, registers asset references, licenses and settlement events through Ethereum contracts, and associates transaction principals using DIDs. Content storage and on-chain registration bear different responsibilities, and a DID association does not automatically prove real-world identity or the attribution of all rights. Selective delivery organizes raw materials, query services, results, Memory Packs, experience packs, Skills and training datasets according to an asset description manifest; the manifest binds definite versions, sources, rights statements, ciphertext references, license scope and delivery policy.

Assetization selects objects and the recipient scope through an asset policy pre-signed by the Owner. The policy configures three distribution scopes separately, team sharing, partner licensing and open marketplace, and limits reading, training, redistribution, validity period, price and dispute rules.

Delivery uses the following order:

{% stepper %}
{% step %}
## Eligibility pre-check

Verify eligibility before delivery.
{% endstep %}

{% step %}
## Quote, payment verification and settlement

Provide a quote, verify payment and complete settlement.
{% endstep %}

{% step %}
## Authorization recheck

Recheck authorization after settlement.
{% endstep %}

{% step %}
## Key wrapping and receipt of delivery

Wrap the key and issue a delivery receipt.
{% endstep %}
{% endstepper %}

When payment is made but non-delivery is confirmed, compensation policy applies. Contract calls use an independent `execute_external` authorization, and exports and sharing check the `export` and `share` scopes separately.

{% hint style="info" %}
Ordinary personal memory does not enter the marketplace by default; successful payment and permitted access are two independent conditions; and plaintext that has already been downloaded cannot be recalled remotely through revocation. ERC-7829 is used only for state assets that need on-chain representation and is not the default container for all state objects.
{% endhint %}

## 6.3.3 DataDrive, Smart Layer and the Storage API

DataDrive / Drive3 / EthDrive / LensDrive are MEFS-based file application entry points, of which LensDrive serves the Lens social ecosystem. The Vault import adapter reads files, social content and knowledge bases from these entry points, automatically validates source permissions, content integrity and interface versions, and then maps objects to State Objects carrying provenance and policy references.

The import configuration limits namespaces, field mappings, sync direction and deletion propagation rules; objects with incompatible formats or insufficient source permissions go into a quarantine queue.

Smart Layer / Storage Middleware provides DID login, upload, download, deletion, file listing, space and traffic, fileDNS permissions and purchase interfaces. Gateway and Vault map these file services onto identity delegation, object versions, policy enforcement and metering through an adaptation layer, with the following relationships:

| Base interface          | State service responsibility        | Mapped fields and controls                                                                             |
| ----------------------- | ----------------------------------- | ------------------------------------------------------------------------------------------------------ |
| DID login, signature    | Gateway identity and authentication | Agent DID, `instance_id`, `run_id`, independent binding and delegation, replay prevention              |
| Upload / download files | Vault object reads and writes       | `namespace`, `schema_version`, `version`, `parent_refs`, `provenance`                                  |
| fileDNS / Reader        | Gateway policy                      | Separation of purpose, term, revocation, training and resharing                                        |
| File hash               | Proof object commitment             | `commitment.algorithm`, `commitment.scope`, `commitment.value`, receipts, audit roots and proof status |
| Space and traffic       | Service quota                       | Explainable metering of Vault objects and the MEFS backend                                             |

The Storage API manages the file interface and the state interface under independent versions. Clients resolve ports, `base_url`, token audiences, concurrency limits, replay windows, expiry and refresh rules, pricing units and price versions from the signature-verified service and price registry, and validate the service identity, version and network domain at connection time.

`request_id` identifies a request attempt, `operation_id` identifies the same retryable business operation, and `receipt_id` identifies a receipt; none is mixed up with `run_id`.

Interface migration uses explicit compatibility windows, conversion rules and routing configuration; the entry point is switched after automated contract tests and object validation pass, and historical objects and their interface semantics remain traceable.
