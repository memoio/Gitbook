# 9 Capability Profiles, Support and Community

The MEMO Agent State Layer offers three composable capability profiles: **the Basic profile provides controllable memory, the Collaboration profile provides portable state and proofs, and the Ecosystem profile provides interface adaptation and selective delivery**. Profile relationships express module dependencies and service scope; the deployment system assembles components from a signed manifest, loads policies and runs automated validation.

| Profile domain                                       | Responsibilities and components                                                                | Configuration and automated validation                                                            |
| ---------------------------------------------------- | ---------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| Protocol and base components                         | Protocol, storage, identity, verification and execution capabilities                           | The registry binds component versions, protocol parameters, networks and service endpoints        |
| Basic profile: controllable memory                   | Vault, candidate admission, provenance, versions, import / export and local receipts           | Checks the object schema, keys, admission policy, recovery and permission-denial paths            |
| Collaboration profile: portable state and proofs     | Identity continuity, Checkpoint, Handoff, Trace, receipts and Proof                            | Checks dependencies, recipient authorization, envelope mapping, evidence chain and fault recovery |
| Ecosystem profile: adaptation and selective delivery | Framework and protocol adapters, enterprise connectors, asset purpose licensing and settlement | Matches the Compatibility Profile and validates rights, budget and delivery policy                |
| Maintenance and service configuration                | Release records, entry discovery, diagnostics, support and security response                   | Signed distribution, health probes, redacted diagnostics, incident grading and rollback           |

_Figure 31: The Three Capability Profiles and How They Compose_

<figure><img src="/broken/files/CghjUr1wVOKw7wyJ8W8r" alt=""><figcaption></figcaption></figure>
