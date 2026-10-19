# 4 Developer Guide

This chapter is for agent, application and platform developers. It describes the integration flow for long-running tasks, the state read and write interfaces, adaptation of frameworks and memory engines, and the mechanisms for recovery, sharing, revocation, deletion and data encryption key rotation. Local validation and service deployment reuse the same object, authorization and receipt semantics; environment differences are expressed through the signed service manifest, key policy, backend parameters and adaptation configuration, and are checked automatically at load time.

{% hint style="info" %}
**Scope: object schemas, interface contracts, adapters and automated validation flows.** Clients resolve service addresses, SDK packages, protocol versions, tool schemas and credential audiences from the signed service registry, and call the interfaces after completing signature verification, version negotiation and scope checks. The parameters in the examples in this document are injected by the corresponding environment configuration and service return values.
{% endhint %}
