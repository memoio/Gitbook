# 2 Core Products

This chapter answers four core questions: who provides the underlying network capabilities, how state objects are stored, under what authorization an Agent accesses them, and how key facts are verified. The answers correspond to one protocol foundation and three Agent products: MEMO Protocol is the common protocol foundation; Vault, Agent Gateway and Proof are the three product entry points for agent scenarios.

Their division of labor can be remembered in one sentence: **Protocol provides network capabilities, Vault manages state, Gateway manages permissions, and Proof manages evidence.**

{% hint style="info" %}
**Scope**: the network responsibilities of Protocol and MEFS, and the object, authorization and evidence interfaces of Vault, Gateway and Proof. Components publish interface versions, backend types, policy entry points and verification parameters through a signed service manifest, and clients use it to complete configuration loading and protocol negotiation; for field, version and evidence semantics, see [Core Technical Principles](file:///5907924/preface/core-principles.md).
{% endhint %}

_Figure 05: Collaboration of Gateway, Vault, Proof and the Protocol Foundation_

<figure><img src="/broken/files/LsdiGt28Hh9mL4Cpkj39" alt=""><figcaption></figcaption></figure>
