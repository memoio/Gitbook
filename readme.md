# README

**Product Architecture and Technical Design · 2026-10-16**

> **Vision and mission: let your AI agent remember, pick up where it left off, and stay under your control. Its memories, half-finished work and results carry over even when you switch models, and you always hold the keys.**

## Overview and Reading Guide

The MEMO Agent State Layer is portable state infrastructure for long-running agent tasks and cross-application collaboration. It uses MEMO Protocol as the protocol and network foundation; Vault, Gateway and Proof organize state objects, controlled access and verifiable evidence respectively; and unified identity, delegation and state specifications connect the storage, computation and settlement components. A unified object schema and adapters decouple business state from any particular model or framework; the recovery process validates objects, the execution environment and external dependencies against compatibility rules, and resumes the task state that can be recovered.

The handbook covers product positioning, architecture and the state model, interface contracts, deployment configuration, security and operations, standards adaptation, economic mechanisms and automated validation rules. Components run together through versioned schemas, signed registries and policy configuration; configuration loading, access authorization, state admission, evidence verification and failure handling form a complete technical loop.

### How to Read This Handbook

| Reader / purpose                              | Suggested reading path                                                                                                                                                                                                                                                                                                |
| --------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Project leads, architecture and product teams | Start with [Core Technical Principles](preface/core-principles.md), then [Chapter 2 on product boundaries](02-core-products/), [Chapter 3 on architecture](/broken/pages/291bbde7baf073966e8a5811d6c89a5b322cf0cd) and [Appendix B, Technical Specifications and Configuration Contracts](appendix/appendix-b.md)     |
| First-time readers                            | Begin with [Chapter 1](/broken/pages/5cd6c2402f21c42682b4052c36dc0beecb629ac6) for the project positioning, product map and role entry points                                                                                                                                                                         |
| Agent / application developers                | [2.2 Vault](02-core-products/2-2-vault.md) → [2.3 Gateway](02-core-products/2-3-gateway.md) → [3.1 Overall Architecture](/broken/pages/56788c697ed65c721284fbf538c590136102e549) → [4.1 Quick Start](04-developer-guide/4-1-quickstart.md) → [4.2 Integration and Adaptation](04-developer-guide/4-2-integrations.md) |
| Storage providers / node participants         | [2.1 Protocol](02-core-products/2-1-protocol.md) → [6.1 Protocol and MEFS](/broken/pages/6cc08026224040de62a73d57b9be7d69ba5bd62d) → [8.3 Tokenomics and Governance](/broken/pages/1bf46ab6f26b68e347db327b831047d8965de235)                                                                                          |
| Enterprise and ecosystem partners             | [2.4 Proof](02-core-products/2-4-proof.md) → [Chapter 5 on security and operations](/broken/pages/8ba866d0de5d3444fd5724e393c5969677da479a) → [6.2 Meeda and Computing Infrastructure](/broken/pages/38f63f530c2d42afbd8587ecd1cc46e0401c4e18) → [Chapter 7 on standards integration](07-standards/)                  |
| Users of earlier MEMO versions                | See [6.3 Application Entry Points and State Access](/broken/pages/75d0183b9f0559f66ec170b974da1805c0e1d307) for product responsibilities and migration boundaries, then [9.3.2 Product Mapping and Documentation Migration](/broken/pages/e94515dab1b27c9db9b1b7068a3224e8ebf0efdb#c9-3-2)                            |
| Integration and operations engineers          | Use [Appendix A, Terminology, Interfaces and Automated Validation Rules](appendix/appendix-a.md), and load and validate the deployment configuration according to the [Appendix B configuration contract](appendix/appendix-b.md#b-1)                                                                                 |

### Overall Product Relationships

**MEMO Protocol is the common foundation; Vault, Gateway and Proof take on state management, controlled access and evidence organization respectively.** MEFS, Meeda, Data Wallet, Data Market and DataDrive connect according to their responsibilities for storage, data availability, user control and data delivery. The deployment configuration selects the corresponding backends and extension components, and the registry provides component discovery, version constraints and connection parameters.

![Figure 01: Relationship Between MEMO Agent State Layer Products and Infrastructure](.gitbook/assets/.gitbookassetsfig01.png)

_Figure 01: Relationship Between MEMO Agent State Layer Products and Infrastructure_

Users manage identity, keys and delegation through the control entry point; an Agent or application sends requests through the managed Gateway. Vault is responsible for objects and storage backends, and Proof organizes the evidence related to a defined claim; the chain carries only the necessary registrations, commitments, verification and settlement. The connections in the figure do not mean that every deployment has the same key custody, fault isolation or proof capability; see [3.3](/broken/pages/4b5ba5c3f324fd8f67b7cb852b6b5360ab84b647) for the precise boundaries.

### Product Positioning, Deployment Configuration and Interface Contract

| Dimension                | Technical definition                                                                                                                   | Operating mechanism                                                                                                                                |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| Product positioning      | Protocol is the protocol foundation; Vault manages state; Gateway provides controlled access; Proof organizes evidence                 | Component responsibilities and interfaces are declared in the registry and combined by deployment configuration                                    |
| Deployment configuration | Local, Hosted and MEFS backends; key custody mode, decryption location, isolation and retention policy                                 | A signed configuration is loaded at startup, and dependencies, identity, backend capabilities and security constraints are validated automatically |
| Interface contract       | API, SDK, schema, protocol version, transport, authentication mode and feature set                                                     | Adapters negotiate according to the capability manifest; a mismatched request returns a deterministic error and existing state is preserved        |
| Automated validation     | Schema validation, signature and delegation verification, version consistency, idempotency, failure recovery and evidence verification | Results are bound to the component version, configuration digest and test conditions, and serve as runtime admission and observability records     |

Product descriptions define responsibilities, inputs and outputs, constraints and exception handling directly. Chapter 4 describes the invocation and recovery flows, and Chapter 8 defines the object schema, interface mappings, registry and metering rules; deployment resolves versions, addresses and parameters from the signed registry and establishes compatibility among components through automated checks. External standards are mapped by adapters onto MEMO's identity, authorization, state and settlement models.
