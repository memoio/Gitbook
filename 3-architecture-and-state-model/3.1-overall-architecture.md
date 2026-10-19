# 3 1 overall architecture

## 3.1.1 Control Plane, Data Plane and Proof Plane

The MEMO Agent State Layer assigns decision, storage and proof to different responsibilities. Adapters use unified object, delegation and provenance interfaces; when a memory engine or storage backend is replaced, field mapping, permission preservation and migration consistency validation are performed, and business writes and proof processing use independent statuses. Gateway enforces policy on requests that pass through the managed entry point; external copies or third-party systems that bypass that entry point remain within their own independent scope of control.

_Figure 09: Responsibilities and Data Paths of the Control, Data and Proof Planes_

| Plane         | Main components                     | Main responsibility                                                                      | What it is not responsible for                                                                                                                                |
| ------------- | ----------------------------------- | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Control plane | MEMO Agent Gateway                  | Identity, delegation, authorization, revocation and signed receipts for managed requests | Long-term storage of state plaintext is not its responsibility; the decryption process and the scope of plaintext visibility are bounded by deployment policy |
| Data plane    | Vault + Local / Hosted / MEFS       | State objects, ciphertext, namespaces, versions and storage                              | Provides reads and writes within the authorized scope and does not expand access permissions on its own                                                       |
| Proof plane   | MEMO Proof + verification contracts | Audit roots, object commitments, source statements and storage proofs                    | Does not prove that all content and conclusions are correct                                                                                                   |

Controlled processing exists as a side path: retrieval, summarization, redaction, trace conversion, and compute-platform or TEE computation must first be authorized by the control plane and then access the data plane with the minimum scope. Processing results are written back to state objects, and input and output digests, external action status and evidence references go into restricted audit records; the complete plaintext is not copied to the proof plane by default.

| Deployment mode | Trust conditions to configure and verify                                                                                                                                               |
| --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Local           | The local key holder, decryption process, file permissions, receipt issuer and log storage location; co-location of components does not imply independent isolation                    |
| Hosted          | The scope of control of the user or enterprise key system and the service provider, the decryption environment, administrator privileges, receipt issuance and audit access boundaries |
| MEFS backend    | The encryption location before upload, the content key custodian, the controlled read path, and the nodes, verifiers and time window of the storage proofs                             |

Independent deployment forms finer isolation boundaries through separate service accounts, minimal file and network permissions, independent key domains and audit access rules. Admission to controlled computation additionally validates the environment identity and proof material; the deployment manifest records these control points, and automated checks verify that the configuration is consistent with the runtime environment. The three-plane figure itself only expresses responsibilities.

Automated architecture validation covers three invariants: a managed request verifies identity and authorization before accessing plaintext; replacing a backend or memory engine preserves the specified object, provenance and permission semantics; and the business write result and the evidence processing result are returned separately. A migration switchover is gated by manifest validation and persistence conditions, and a delay in proofs does not change the result of business that has already completed.

## 3.1.2 The Six-Layer Agent State Layer

The six-layer model breaks a state request into six kinds of responsibility and gives how they carry over to the six capability domains of MEMO Protocol. The layers collaborate through explicit inputs and outputs, policy references and failure statuses, and adaptation validation separately checks object encapsulation, delegation propagation, backend storage, evidence association and the scope of controlled processing.

| Layer                        | Responsibility                                   | Main objects or mechanisms                                 | Product carrying it |
| ---------------------------- | ------------------------------------------------ | ---------------------------------------------------------- | ------------------- |
| L1 State Object              | Defines what is stored                           | Memory, Task State, Artifact, Memory Pack                  | Vault               |
| L2 State Storage             | Defines how it is stored                         | Encryption, Version, Local / Hosted / MEFS                 | Vault + MEFS        |
| L3 Identity & Delegation     | Distinguishes who is acting and on whose behalf  | User DID, Agent DID, instance\_id, run\_id, Delegation     | Gateway             |
| L4 Access & Lifecycle        | Decides what can be done                         | Policy, purpose, term, revocation, deletion                | Gateway + Vault     |
| L5 Provenance & Verification | Explains where it came from and how to verify it | Receipt, Audit Root, Attestation, Storage Proof            | Proof               |
| L6 Controlled Processing     | Processes state after authorization              | Retrieval, Redaction, TEE, compute platform, Memory Engine | Optional extension  |

_Figure 10: Main Path and Optional Processing of the Six-Layer Agent State Layer_

The six layers represent a responsibility model for state requests and keep a mapping to the six network capability domains of MEMO Protocol. The synchronous main path for a managed state read or write is organized as L3 → L4 → L1 → L2; after the receipt is saved locally, L5 proof aggregation and anchoring are handled asynchronously. L6 participates only when processing is genuinely needed and independent authorization has been obtained, and it is not a mandatory path for every request.

## 3.1.3 The Complete Path of an Agent Request

The following gives the path of a managed request that passes through Gateway. Both allow and deny decisions go into the minimal receipt log, and local persistence and background batch anchoring are handled separately. Gateway registers a request identifier for each accepted request and automatically reconciles the business operation ledger with the receipt log; when a gap is found, it recovers a verifiable record or retains an exception status. An inclusion proof verifies only the membership in a specified batch. This figure shows the path in which the receipt is saved locally with success; a log write failure is handled under an independent receipt status.

_Figure 11: Sequence of a Managed Agent Request, Business Result and Asynchronous Proof_

The complete steps:

{% stepper %}
{% step %}
### Submit the request

The Agent submits the Agent DID, `instance_id`, `run_id`, the user delegation, the target namespace, the operation and the purpose through MCP, REST or SDK; `request_id` identifies a request attempt, and `operation_id` identifies the same business operation that can be retried idempotently.
{% endstep %}

{% step %}
### Verify identity and delegation

Gateway verifies the user signature, the Agent identity, the delegation chain, the validity period and the revocation status.
{% endstep %}

{% step %}
### Compute effective permissions

The policy engine computes the effective permissions; a rejected request does not enter state reads or writes, and should save a denial receipt that does not disclose the existence or content of objects. `decision=allow/deny` expresses only the policy decision and does not mean the business execution succeeded.
{% endstep %}

{% step %}
### Access authorized state

For an allowed request, Vault locates the object, version and storage backend, and returns only the ciphertext or necessary fields within the authorized scope.
{% endstep %}

{% step %}
### Perform controlled processing when needed

If processing is needed, L6 performs retrieval, summarization, redaction or computation after obtaining independent permission, and discloses the decryption and processing environment. The visibility of plaintext in a controlled environment cannot be presumed to be zero merely because of "ciphertext storage."
{% endstep %}

{% step %}
### Save the receipt

Gateway should generate and locally save a signed receipt binding the request and business operation, the principal, the policy version, the object version, the commitment and the execution status. A receipt expresses an event statement made by the issuer, and its scope of trust also depends on the record source and the verification mechanism.
{% endstep %}

{% step %}
### Aggregate and anchor proofs

Proof aggregates receipts asynchronously and anchors audit roots according to policy; source statements, storage proofs and state registration complete under their own mechanisms. That proof material can be obtained does not mean it has been verified successfully.
{% endstep %}
{% endstepper %}

A response expresses `business_status` (`not_executed/succeeded/failed/unknown`), `receipt_status` (`available/pending/failed`) and `proof_status` (`not_requested/pending/available/failed`) separately. A paid request additionally lists the normalized `payment_status`. When a write succeeds while the receipt is still in the recovery queue, `business_status=succeeded` is kept and `receipt_status=pending` is returned; if recovery fails, it is marked `failed`. The log and proof services recover evidence through operation identifiers without repeating business that has already succeeded. When the business result is `unknown`, it is queried and reconciled by `operation_id`, and then, according to the result found, enters the completion, compensation or controlled retry path.

Request invariants: an unauthorized managed request must not touch plaintext; the receipt statement must be consistent with the known business result; repeated attempts reuse the corresponding `operation_id` while recording `request_id` separately; and when the result of an external action is unknown, it cannot be retried unconditionally.
