# 5 Security, Governance and Operations

MEMO's security governance covers user identity, file ownership, retention verified against agreement, and Agent state control. Agent scenarios need to distinguish "who owns the data," "which Agent and instance is acting," and "who granted the permission," and to retain scoped evidence for memory provenance, runtime content, tool results and external side effects. Identity, authorization, data protection and auditing together form the security control system; the policy engine, the key enforcement point and the evidence verifier respectively perform admission, key release and evidence verification, and control results are written into versioned receipts.

{% hint style="info" %}
**Product positioning: an identity, authorization and risk control system for the entire lifecycle of agent state.** The Owner signs delegation scopes and policy configuration in advance, and Gateway validates them automatically on every managed request and before actual delivery; source admission, risk handling, acceptance and failure recovery use executable rules and record their results with independent business statuses and evidence statuses.
{% endhint %}
