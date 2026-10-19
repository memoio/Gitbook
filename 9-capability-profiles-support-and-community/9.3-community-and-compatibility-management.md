# 9 3 community

## 9.3.1 Contribution Process and Service Entry Points

Contribution types include documentation, code, adapters, schemas, validation cases, research models and node configurations. Repositories and contribution rules are discovered through the signed Official Links registry; the change pipeline runs the following steps by contribution type:

{% stepper %}
{% step %}
## Verify contribution prerequisites

Verify the repository, branch, license, contribution agreement, code of conduct and commit identity.
{% endstep %}

{% step %}
## Associate an Issue or architecture record

Associate an Issue or architecture record, declaring the problem, boundaries, configuration and migration impact.
{% endstep %}

{% step %}
## Validate code

For code, run builds, unit and integration validation, dependency scanning and interface diff checks.
{% endstep %}

{% step %}
## Perform enhanced validation

For schemas, signing domains, encryption formats, economic parameters and contracts, run enhanced validation, simulation and policy threshold checks.
{% endstep %}

{% step %}
## Generate and cross-check documentation

Generate terminology, examples, diagrams, compatibility descriptions and release records, and cross-check content references.
{% endstep %}

{% step %}
## Authorize, release and retain rollback

After the authorization policy and automated validation both pass, generate signed artifacts, update the registry, roll out assembly per the deployment policy and retain a rollback point.
{% endstep %}
{% endstepper %}

Documentation uses unified terminology and numbering, diagrams and drawing prompts share node and relationship definitions, and examples use non-sensitive parameters. The Chinese and English editions reference the same glossary, schemas and service manifest; the checker automatically validates internal links, configuration parameters, JSON / YAML formats and figure number correspondence.

The adapter manifest records the maintainer, license, dependencies, protocol and schema versions, permissions, validation scope and service responsibility. The release pipeline builds artifacts with locked dependencies and a test configuration, while the runtime still performs authorization, required-capability matching and health checks. Maintenance responsibility, feature matching and runtime health are expressed separately.

Official Links manages the entry points for code, services, support, security and billing through a versioned registry. Each entry contains a URL, purpose, environment, responsible role, authentication method, validity period, signature and associated configuration; a client first validates the registry and then accesses the specified entry point by purpose.

| Service entry point                 | Registry key                | Configuration and discovery rules                                                     |
| ----------------------------------- | --------------------------- | ------------------------------------------------------------------------------------- |
| Official Links root entry           | `official_links`            | Trusted root, registry version, release signature and validity period                 |
| Code, releases and contributions    | `repositories` / `releases` | Repositories, branches, licenses, contribution rules and artifact signatures          |
| Deployment and examples             | `deployment` / `examples`   | Environments, component manifests, access conditions, sample data and validation jobs |
| Network explorer and status page    | `explorer` / `status`       | Network identifiers, endpoints, monitoring scope and health rules                     |
| Technical support and community     | `support` / `community`     | Authentication method, accepted scope, responsible roles and response policy          |
| Private security feedback           | `security`                  | Encrypted reporting address, recipient key, incident receipt and disclosure rules     |
| Partnership and enterprise services | `enterprise`                | Service scope, onboarding conditions, contact endpoints and identity verification     |
| Pricing, quotas and billing         | `pricing`                   | PriceBook references, units, assets, quotas, versions and effective scope             |

The service registration job validates the URL, certificate, environment and associated version; security and billing entries use signed entries restricted to specific purposes. Address updates notify clients through signed versions, and entries that are expired or fail validation stop being used and return an error, to avoid replacing an endpoint from an untrusted source.

## 9.3.2 Product Mapping and Documentation Migration

Each product connects to the Agent State Layer as shown in the table below. Interfaces, documentation and tools keep continuity through versions and permanent references, and migration jobs map old interfaces onto the target contracts while retaining a recovery path.

| Product / interface domain                | Related sections    | Responsibility mapping and migration rules                                                                                  |
| ----------------------------------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| MEMO Protocol six layers                  | 2.1, 3.1, 6.1       | Provide the protocol capability domains for the state layer, keeping each layer's responsibilities and interface boundaries |
| MEFS User / Provider / Keeper (Guardian)  | 6.1, 8.3            | Handle ciphertext storage, challenges, repair and service settlement for state                                              |
| Data Wallet / MEMO DID                    | 1.2.6, 5.1, 6.3.1   | Connect user control, Agent DID, independent binding and the delegation chain                                               |
| Data Market                               | 1.2.7, 6.3.2, 7.2.3 | Map data, state, knowledge and results to selectively delivered objects                                                     |
| Meeda / DA / Lightnode                    | 1.2.8, 6.2          | Provide Proof with availability evidence that retains protocol assumptions                                                  |
| DataDrive                                 | 6.3.3               | Map the file interface to Artifact and general file operations                                                              |
| Smart Layer / Storage Middleware          | 2.3, 4.2, 6.3.3     | Protocol and tool interfaces connect to Gateway, the SDK and MCP                                                            |
| Compute platform / TEE                    | 3.3.3, 6.2.3, 7.2.1 | Associate controlled processing results with the execution environment and Attestation                                      |
| Storage API / CLI / deployment interfaces | 4, 6, 8             | Connect to the target configuration through version adaptation, parameter mapping and idempotent migration                  |
| Economic model and node incentives        | 8.3                 | Apply parameterized service, proof and settlement rules to state workloads                                                  |

The documentation migration table contains the source path, target path, applicable version and permanent redirect rules. The build process runs link checks and example validation, and the deployment process probes redirects, cache refresh and the target content digest. Historical examples keep their version labels and are placed in a separate index, and the current entry point always uses the commands and configuration that match the Deployment Registry.

## 9.3.3 Technical Extensions and Architecture Records

Extension proposals define interfaces, policies and validation rules through versioned architecture records. The table below gives the mechanism adopted in each extension domain and its automated validation path:

| Extension domain                  | Technical approach                                                                                                                                                                                                           | Automated validation                                                                                                       |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| Agent DID and identity continuity | The DID binds a signing public key and an instance; replacing the key creates a new instance and DID; a dual-signed continuity record or an independent recovery statement links history; the Owner issues a new delegation  | Resolution, proof of control, replay prevention, revocation, recovery credentials and isolation of old and new permissions |
| Trace exchange such as ADP / ATIF | Adapters map events, tools, results and object references by external version, keep references to the original format and trim sensitive fields per policy                                                                   | Schema mapping, reference integrity, round-trip conversion and sensitive field scanning                                    |
| Memory Engine                     | Original sources and State Objects serve as traceable records, indexes reference object versions, candidates are admitted by policy, and deletion events propagate to derived indexes                                        | Consistency of objects and indexes, admission rejection, contamination isolation and migration recovery                    |
| A2A Handoff                       | A Task / Artifact binds the Memory Pack, task identifier, recipient delegation and two-way receipts, and recovers by idempotency records                                                                                     | Cross-organization authorization, package integrity, revocation races, partial failure and deduplication                   |
| Attestation trust                 | The trust registry limits issuers, statement types, validity periods and revocation rules, and the verifier combines evidence by policy                                                                                      | Issuer chain, signature, type scope, validity period and revocation queries                                                |
| Controlled computation            | A TEE or an organization-controlled environment is chosen per task; ZKP proves a defined computation claim, and FHE handles encrypted computation suited to its operators; key release binds the task and environment policy | Environment attestation, image, purpose, input and output scope, resource budget and key release conditions                |
| Selective assetization            | Source licenses, asset ownership, access, training and redistribution are modeled separately, the Owner policy selects objects, and delivery is encapsulated per recipient                                                   | Rights conditions, license scope, sensitive scanning, and the dual gate of payment and authorization                       |
| Node economics and Proof          | Small-object batching, version retention and storage tiering connect to orders; metering and proofs are associated with settlement; parameters are constrained by budget and governance policy                               | Load, recovery, challenges, fake orders, release caps and ledger reconciliation                                            |

An architecture record contains the proposal version, scope, constraints, configuration, validation and rollback, and uses the format below. Parameters are resolved from the corresponding architecture and build manifests; the validator checks required fields, references, schemas and migration cases, and records are archived with signatures and content digests.

```markdown
# ADR-<number>: <decision_title>

- record_state: active | superseded
- date: <date>
- responsibility_roles: <responsible roles>
- architecture_version: <architecture version>
- configuration_refs: <references to policies, schemas, registry and compatibility>
- validation_refs: <references to validation rules, jobs and reports>

## Context
<business problem, dependencies, constraints and risks>

## Design
<adopted mechanism, interfaces, data flow and responsibility boundaries>

## Configuration
<parameter sources, type ranges, default policy and validation rules>

## Security and privacy
<identity, permission, key, data, economic and runtime controls>

## Compatibility and migration
<mappings for schemas, APIs, contracts, clients and historical data>

## Validation and rollback
<automated validation, stop conditions, recovery points and rollback rules>
```

When an architecture record is superseded by a new version, forward and backward references are established and the historical signature is retained. The validation environment uses isolated data and test assets; before an interface enters the build manifest, it undergoes type, dependency, security policy and compatibility checks. The runtime configuration is always bound to the architecture record, artifacts and registry that passed validation, forming a traceable relationship among design, configuration and execution.
