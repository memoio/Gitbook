# README

## 3 Architecture and State Model

This chapter turns product language into technical boundaries: the three planes explain how the system is organized, the six layers explain how responsibilities are divided, and the request path explains how components interact; the state model and trust boundaries define what can be stored, migrated and proved.

The architectural goal is to make the division of decision, storage and proof explicit and to reduce the concentration of privilege in any single component. The division of responsibilities does not by itself constitute a cryptographic or deployment isolation guarantee; the actual boundaries depend on the key holder, the decryption location, operations privileges and how evidence is issued.

{% hint style="info" %}
**Scope: the responsibilities of the three planes, six-layer collaboration, the state object model and trust boundaries.** The network, objects, authorization and controlled processing collaborate through versioned interfaces; the deployment configuration specifies the key custodian, decryption location, service permissions, backend persistence conditions and evidence verification rules.
{% endhint %}
