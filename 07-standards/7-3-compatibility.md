# 7 3 compatibility

## 7.3.1 Protocol and Client Compatibility Matrix

Adapters treat compatibility configuration as an executable contract, pinning the specification identifier, transport, authentication, capability mapping and validation cases. A client first reads the service capability manifest and then chooses the matching configuration according to its own version; when the version, signature, transport or capability does not match, an explicit error is returned, and authentication is not degraded automatically nor business semantics changed.

| Component       | Specification configuration                                                                     | Transport or environment                                                        | Adaptation responsibility                                                                                 | Automated compatibility validation                                                                                                              |
| --------------- | ----------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| MEMO Gateway    | MCP 2026-07-28 configuration; extensions listed separately in the capability manifest           | Local stdio; remote HTTPS carries MCP requests according to configuration       | resources, tools, error mapping, Agent DID and delegation validation, state reads and writes and receipts | Request format, tool schemas, stateless requests, authorization denial, idempotent writes, cancellation and error propagation                   |
| MEFS MCP Server | MCP storage tool configuration, pinning tool and backend interface versions                     | Local process or authenticated remote service                                   | put, get and proof query mapped to Vault backend calls and restricted storage credentials                 | Content integrity, object version binding, backend failures, key boundaries and Proof type verification                                         |
| A2A Adapter     | A2A v1.0.0 configuration                                                                        | HTTPS; streaming updates enabled per task configuration                         | Agent Card, Task, Message, Artifact, cancel and Handoff reference mapping                                 | Identity and receiving delegation, stream interruption recovery, cancellation, duplicate tasks, partial delivery and result persistence         |
| Agent Skill     | Agent Skills format; identified jointly by package hash, package version and Host configuration | A Host with a controlled tool execution environment                             | Save, restore and verification workflows; declares tool and resource dependencies                         | Package signature, field schema, tool permissions, file and network boundaries, execution result receipts                                       |
| x402 Adapter    | x402 V2 configuration, binding payment scheme, network and asset                                | The HTTP 402 payment flow is carried through an authenticated HTTPS entry point | Payment headers, quote, authorization payload, verification, settle-then-deliver and compensation mapping | Quote expiry, duplicate authorization, verification failure, unknown settlement, revocation recheck, delivery failure and refund reconciliation |

The compatibility pipeline records the test suite version, the client and adapter versions, the environment configuration, an input data digest, the time and the item-by-item results, and generates a signed report. The service registry references the corresponding report and the capability manifest; release routing loads only configurations that meet the current admission rules. Official and community clients use the same interface contract, and the maintaining principal, service scope and issue tracking entry point are recorded as independent configuration.

## 7.3.2 Standards Mapping and Adaptation Boundaries

Each external specification corresponds to a defined adaptation responsibility. The specification name, version and reference address are used to bind interface behavior, and external protocol objects keep their native fields; MEMO objects, policies and receipts keep their own versions and the snake\_case field convention.

| Object       | Specification reference                                                              | MEMO adaptation approach                                                                                                                      | Control and verification boundary                                                                                                                                      |
| ------------ | ------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ERC-8004     | [ERC-8004](https://eips.ethereum.org/EIPS/eip-8004)                                  | Associates the external registration, service endpoints, reputation and verification records of an Agent DID with the identity discovery view | Registration and reputation do not grant state permissions; requests are validated by Gateway against independent delegations                                          |
| ERC-7857     | [ERC-7857](https://eips.ethereum.org/EIPS/eip-7857)                                  | Encapsulates private Agent metadata according to the Owner's asset policy and maps transfer, cloning and usage licenses                       | State scope, key recipient, authorized purpose and evidence are validated separately, and ordinary memory stays non-assetized by default                               |
| ERC-7829     | [ERC-7829](https://eips.ethereum.org/EIPS/eip-7829)                                  | Maps the content reference, storage metadata and read policy of a data object, Memory Pack or Artifact to an asset description                | The on-chain reference does not disclose plaintext; reading is jointly constrained by delegation, keys and the content lifecycle                                       |
| MCP          | [2026-07-28 specification](https://modelcontextprotocol.io/specification/2026-07-28) | Maps tool and resource requests to Gateway and Vault, with extensions enabled by the capability manifest                                      | Identity, delegation, version and idempotency association are validated on every request; Host enforcement controls and backend persistence are implemented separately |
| A2A          | [v1.0.0 specification](https://a2a-protocol.org/v1.0.0/specification/)               | Maps tasks, messages and Artifacts to the task envelope, Handoff and state versions                                                           | Task completion, object saving, local receipts and Proof status are recorded separately                                                                                |
| Agent Skills | [Format specification](https://agentskills.io/specification)                         | Organizes operating instructions, scripts, reference materials and assets through skill packages                                              | Package declarations express only dependencies; the Host and Gateway separately enforce code, network and state permission controls                                    |
| x402         | [HTTP 402 documentation](https://docs.x402.org/core-concepts/http-402)               | Uses V2 payment headers; after the Facilitator verifies and settles, Gateway rechecks authorization and delivers                              | Payment status is independent of business, receipt and proof statuses; an unknown settlement is queried first, and a clear delivery failure is compensated by policy   |

A specification configuration records the source URI, content digest, protocol version, and interface and event mappings. A configuration update first runs schema validation, contract tests, permission boundary tests and historical object replay, and then publishes a new signed configuration; clients choose routes by explicit version and do not infer additional capabilities from a specification's name.

## 7.3.3 Versions, Deprecation and Migration Notes

Every external interface uses an explicit version, and the protocol version, schema version, SDK version, service release version and network / contract version are managed separately. They can evolve at different paces, but must be associable within a single receipt.

Interface deprecation and migration use a five-step process:

{% stepper %}
{% step %}
### Define the migration

Record the replacement interface, impact scope, conversion rules and automated validation cases.
{% endstep %}

{% step %}
### Announce

Publish the date new features stop being added, the compatibility window and the migration guide.
{% endstep %}

{% step %}
### Run in parallel

Old and new versions run at the same time, with converters and telemetry provided.
{% endstep %}

{% step %}
### Restrict

Stop new integrations, but do not suddenly break existing tasks.
{% endstep %}

{% step %}
### Retire

Close the entry point, and retain read-only history, validation tools and redirects.
{% endstep %}
{% endstepper %}

Breaking changes must cover at least identity representation, signing domain, permission semantics, object schema, encryption format, transport, error codes, payment scheme and Proof verification. A state manifest and validation results should be generated before and after migration, preserving `object_id`, direct parent version relationships, provenance and policy references. When ciphertext is migrated as-is, commitments under the same digest scope can be compared; when re-encrypted or format-converted, the digest may change, and the old-to-new envelope mapping and the conversion receipt must be recorded, and "the digest is necessarily identical" cannot be used as the acceptance condition for all migration methods.

When the Agent identity signing key is replaced, a new instance and a new Agent DID are created, the old and new instances each sign the continuity record, and the record is validated automatically by the Owner delegation policy; when the old key is unavailable, an independent recovery statement from a preset recovery authorization is used. Existing reputation and historical credentials keep their original DID attribution and are presented through associated queries; the new DID uses a new delegation signed by the Owner, and bindings and permissions are not copied automatically. A controlled recovery of the same `instance_id` with the original key keeps the DID, while creating a new concurrent instance uses an independent key and DID. Content encryption key rotation changes only the data encryption and key wrapping relationships and does not create a new Agent DID.

When an external protocol deprecates an old transport or field, MEMO should write the impact into the compatibility matrix and give the last supported version and date. When clients migrate through a configured compatibility proxy, the proxy pins the cutoff time, interface scope and conversion rules and enforces the same or stricter authentication and authorization; a configuration that lowers the control level is rejected at load time.

## Official References

* [ERC-8004: Trustless Agents](https://eips.ethereum.org/EIPS/eip-8004)
* [ERC-7857: AI Agents NFT with Private Metadata](https://eips.ethereum.org/EIPS/eip-7857)
* [ERC-7829: Data Asset NFT](https://eips.ethereum.org/EIPS/eip-7829)
* [Model Context Protocol 2026-07-28 Specification](https://modelcontextprotocol.io/specification/2026-07-28)
* [MCP 2026-07-28 Key Changes](https://modelcontextprotocol.io/specification/2026-07-28/changelog)
* [Agent2Agent Protocol v1.0.0 Specification](https://a2a-protocol.org/v1.0.0/specification/)
* [Agent2Agent Protocol latest entry](https://a2a-protocol.org/latest/)
* [Agent Skills Specification](https://agentskills.io/specification)
* [x402: HTTP 402](https://docs.x402.org/core-concepts/http-402)
* [x402: Client / Server](https://docs.x402.org/core-concepts/client-server)
* [x402: Facilitator](https://docs.x402.org/core-concepts/facilitator)

The adaptation configuration binds the official references above to content digests of the specifications. A version update is validated by the automated compatibility pipeline for interfaces, authentication, field mappings and historical replay results, and the signed verification report is associated with the service capability manifest.
