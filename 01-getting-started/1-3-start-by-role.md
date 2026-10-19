# 1 3 start by role

## 1.3 Getting Started by Role

Different readers come to this document with different questions. The reading paths below are organized by three main roles, and each path corresponds to one kind of evaluation or usage goal.

_Figure 04: Choosing a MEMO Reading Path by Role_

### 1.3.1 Agent / Application Developers

Recommended path: MEMO Agent Gateway → Quick Start → reading and writing state through the SDK / REST → MCP and framework adaptation → the Vault object model → Proof receipt verification.

The automated validation loop for developer integration consists of:

{% stepper %}
{% step %}
### Record identities

Record the user, the Agent DID, `instance_id` and `run_id` separately.
{% endstep %}

{% step %}
### Create a delegation

Create a delegation limited to a namespace, operations, term and purpose.
{% endstep %}

{% step %}
### Write and read state

Write and read a state object that carries a version and provenance.
{% endstep %}

{% step %}
### Verify authorization boundaries

Verify that a request outside the authorization is rejected without touching plaintext.
{% endstep %}

{% step %}
### Export and check receipts

Export the allow and deny receipts and check the audit references.
{% endstep %}

{% step %}
### Revoke and verify rejection

After revoking the authorization, access again, and automatically verify that subsequent managed requests are rejected.
{% endstep %}
{% endstepper %}

Local Vault + Gateway forms the local deployment combination for this loop. The launcher loads component versions, schemas and installation parameters from the signed service manifest, and runs checks on dependencies, key management, policy consistency and failure recovery; once the checks pass, the corresponding interfaces are opened, and on failure the configuration item and the error stage are returned. MEFS nodes are connected separately through the decentralized storage configuration.

### 1.3.2 Storage Providers / Node Participants

Recommended path: MEMO Protocol → MEFS network roles → node deployment → storage orders → redundancy and repair → storage proofs, challenges and node economics.

Nodes take part in storage, verification and settlement through MEFS and Protocol, and Vault maps state ciphertext and retention requirements onto storage orders. Agent state workloads are described by object size, version frequency, retention period, read volume and proof frequency, and batching and tiered storage configurations control the overhead of small objects and high version rates. A node's runtime configuration binds the network version, resources and ports, staking parameters, fees, health checks, upgrade rollback and key policy; metering data feeds into settlement records and capacity planning.

### 1.3.3 Enterprise and Ecosystem Partners

Recommended path: MEMO Proof → Meeda / DA → FileProof and contracts → identity and delegation → standards composition → compatibility and deployment configuration.

The integration configuration covers data availability, state residency, key custody, audit requirements, proof mechanisms, failure responsibility, controlled processing, external side effects and settlement boundaries. Meeda organizes commitment submission and verification through the Lightnode API, and interface versions are matched through compatibility descriptors. Enterprise policy defines the responsibilities of organization administrators, data owners, Agents and external partners separately, and binds rules for export, deletion, retention periods and incident response.
