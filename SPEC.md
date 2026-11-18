# KORS Specification

| | |
|---|---|
| **Version** | 0.1 (first public draft) |
| **Date** | 18 November 2026 |
| **Author** | Sean Lamet, for Dekrow |
| **License** | [CC BY 4.0](LICENSE) |
| **Canonical source** | https://github.com/dekrow-corp/kors |
| **Language** | English is the normative text. Translations are listed in [translations/](translations/). |

## Status of this document

This is the first public version of the KORS specification. It is a draft: the five criteria are stable, their detailed requirements may still change before version 1.0. Every change is recorded in the [CHANGELOG](CHANGELOG.md). Feedback is welcome through the issue tracker of the canonical repository; see [CONTRIBUTING.md](CONTRIBUTING.md).

## 1. Purpose and scope

KORS (Kernel for Operations and Resources System) names an architecture property of a set of production management applications: MES, QMS, CMMS, DMS, PLM and any other application at Level 3 of ISA-95. It is not a product, not a technology and not a trademark.

A system may call itself KORS if it satisfies the five criteria of section 4. All five, not four out of five.

KORS constrains how applications are built so that they never need to be integrated with one another. It does not describe how to connect existing software after the fact, how to collect machine data, or how to organize a plant-wide data space. Those problems have their own answers, several of which are listed in section 11.

Sections 2 to 7 are normative. Sections 8 to 13 are informative.

## 2. Conformance language

The key words MUST, MUST NOT, REQUIRED, SHALL, SHALL NOT, SHOULD, SHOULD NOT, RECOMMENDED, MAY and OPTIONAL in this document are to be interpreted as described in BCP 14 ([RFC 2119], [RFC 8174]) when, and only when, they appear in all capitals.

Each requirement has a stable identifier (C1.1, C3.4...). Identifiers are never reused: a removed requirement leaves a gap.

## 3. Terminology

- **System**: the set of services, operated together for one organization, for which a KORS conformance claim is made.
- **Service**: an independently deployable unit that carries one business capability (work order tracking, non-conformance management, maintenance...).
- **Owner**: the only service allowed to modify a given piece of data. Other services read it or receive it.
- **Event**: a past, immutable business fact published by the owning service ("work order started", "part scrapped", "machine down").
- **Contract**: the published, versioned description of an exchange: schema, semantics and compatibility rules.
- **Common vocabulary**: the set of types and identifiers used in contracts for entities shared between services.
- **Extension**: a named addition to the common vocabulary, typically for an industry sector, that does not modify the core.
- **Boundary**: the point where a system that is not KORS exchanges with a KORS.
- **Adapter**: a service that sits on a boundary and translates between an external system and the common vocabulary.
- **Customer**: the organization whose production the system manages and whose data it holds.
- **Vendor**: the organization that supplies one or more services. A system may have several vendors, and the customer may be its own vendor.

## 4. The five criteria

### C1. Event-based communication

- **C1.1** Every change of business state that is useful outside a service MUST be published as an event by its owner.
- **C1.2** Every event type MUST be described by a published, versioned contract.
- **C1.3** Synchronous reads between services are permitted. A service MUST NOT depend on the availability of another service to perform its main function.
- **C1.4** Batch file synchronization between services MUST NOT be used.
- **C1.5** The transport is not prescribed (MQTT, NATS, Kafka, AMQP or any other).
- **C1.6** Every event MUST carry at least: a unique identifier, its type, the version of its contract, the time the fact occurred, and the identity of the publishing service.
- **C1.7** Consumers SHOULD tolerate duplicated and out-of-order events. The delivery guarantees a service offers SHOULD be stated in its contracts.

Note: C1.4 applies between services. An adapter (section 5) may read or write files when that is the only interface an external system offers; what it exchanges with the other services is events.

### C2. Common vocabulary at the boundaries

- **C2.1** Contracts MUST express shared entities (equipment, material and lot, personnel, work order, process segment, non-conformance...) in a common vocabulary.
- **C2.2** This vocabulary SHOULD derive from ISA-95 / IEC 62264 (Parts 2 and 4). Every deviation MUST be documented.
- **C2.3** Each service remains free in its internal model and its persistence. There MUST NOT be a database shared between services.
- **C2.4** Identifiers of shared entities MUST be stable and unambiguous from one service to another.
- **C2.5** Sector extensions (EN 9100, IATF 16949...) MAY enrich the vocabulary as named extensions, without modifying the core.

### C3. Autonomy of each service

- **C3.1** Each service MUST be deployable, upgradable and restartable without redeploying the others.
- **C3.2** The failure of one service MUST NOT stop the others. Degraded behavior SHOULD be documented.
- **C3.3** Each service MUST own its data.
- **C3.4** An incompatible change to a contract MUST result in a new version. The previous version MUST remain served for a documented period.
- **C3.5** Consumers SHOULD ignore fields they do not know, so that a compatible addition to a contract does not break them.

### C4. Modular adoption

- **C4.1** Each service MUST deliver value that is usable on its own.
- **C4.2** Adding a service MUST NOT require modifying the existing services: the new service subscribes to the events it needs.
- **C4.3** Removing a service MUST NOT break the others beyond the functions that depend on it explicitly and in a documented way.

### C5. Open data

- **C5.1** The vocabulary and the contracts MUST be documented and accessible to the customer without condition.
- **C5.2** The customer MUST be able to export all of its data in a documented format, without intervention from the vendor.
- **C5.3** A documented access API MUST exist. It SHOULD follow an open standard where one covers the need (for example i3X for access to contextualized data).
- **C5.4** The system MUST be hostable on an infrastructure chosen by the customer.
- **C5.5** The end of the contractual relationship, whatever its cause, MUST NOT deprive the customer of its data. The export of C5.2 MUST remain available after the contract has ended: for a documented period when the vendor hosts the system, and without time limit when the customer hosts it.

## 5. The boundary

A KORS rarely lives alone: the plant has an ERP, machines, sometimes a historian or an existing PLM. These systems are not KORS and do not have to be.

- An external system is integrated through a dedicated **adapter**, which translates its data to the common vocabulary and back (an anti-corruption layer).
- The adapter is a service like any other: it MUST satisfy C1 to C5.
- An existing UNS MAY serve as a transport or as a source. A KORS MAY publish to a UNS.
- The exact promise: **nothing to integrate between KORS services, one single boundary to maintain per external system.**

## 6. What KORS does not require

- A particular technology, broker, language, cloud, identity provider or orchestrator.
- A single vendor: two services from two different vendors can form a KORS.
- That the code of the services be open source. Only the data, the vocabulary and the contracts must be open.
- The presence of a UNS.
- A particular contract description format. CloudEvents, AsyncAPI, Protocol Buffers, JSON Schema or any other documented format is acceptable.

## 7. Conformance

- **7.1** A system conforms to a given version of this specification if it satisfies every MUST and MUST NOT requirement of sections 4 and 5. There is no partial conformance: a system that satisfies four criteria out of five is not KORS.
- **7.2** A requirement expressed with SHOULD may be left unsatisfied only if the deviation and its reason are documented.
- **7.3** A conformance claim MUST state the version of the specification, the scope of the claim (which services, which deployment) and where the evidence of section 8 can be consulted.
- **7.4** Conformance is self-declared. This specification defines no certification body and no mark. A claim MUST NOT state or imply that it has been verified or endorsed by the authors of this specification.
- **7.5** Conformance is a property of a deployed system, not of a single product. A product on its own can be described as "designed for KORS" if it satisfies every requirement that applies to an individual service.

A template for conformance statements is provided in [CONFORMANCE.md](CONFORMANCE.md).

## 8. Checklist

| # | Question | Expected evidence |
|---|---|---|
| C1 | Are state changes published by their owner? | List of published, versioned event contracts |
| C1 | Does a service keep working when another is stopped? | Test: stop one service, verify that the others still perform their main function |
| C1 | Can an event be identified and traced to its source? | Sample events showing identifier, type, contract version, occurrence time and publisher |
| C2 | Do contracts refer to a documented common vocabulary? | Published vocabulary, deviations from ISA-95 listed |
| C2 | Is there a database shared between services? | Deployment diagram: one database per service |
| C3 | Can a service be upgraded on its own? | Upgrade procedure for one service without redeploying the others |
| C3 | What happens when a contract changes? | Versioning policy and support period |
| C4 | Is each service usable on its own? | Demonstration of one service deployed in isolation |
| C4 | Does adding a service require modifying the others? | A service added by subscription only |
| C5 | Can the customer export everything alone? | Documented full export, executable without the vendor |
| C5 | Does the customer choose the hosting? | Documented installation on a third-party infrastructure |
| C5 | What happens to the data when the customer leaves? | Contract terms and export procedure that remain valid after the contract has ended, with the documented period when the vendor hosts |

## 9. Examples

- **Satisfies all five**: an MES and a QMS from two different vendors. Each has its own database, publishes its events under versioned contracts in an ISA-95 vocabulary, and offers a full export. The QMS reacts to "part scrapped" without the MES knowing it exists. The ERP is connected through an adapter.
- **Does not qualify (two out of five at best)**: a monolithic MES plugged into a UNS. Data flows (C1 partly, C5 possibly), but the vocabulary is limited to a topic hierarchy (C2 no), and the functions cannot be deployed separately (C3 and C4 no).
- **Does not qualify**: a "modular" suite from a single vendor whose modules share one database. The modularity is commercial (C4 in appearance), but C2.3 and C3 are violated.

## 10. Rationale

### 10.1 KORS and the Unified Namespace

A UNS is a single namespace, usually an MQTT broker organized along the ISA-95 hierarchy, where every system in the plant publishes and reads. It has become the reference for connecting machines, sensors and existing systems. It says nothing about how applications are built: a monolithic ERP, a closed MES and a spreadsheet can all be plugged into it. Data will flow; the applications remain what they were.

KORS places the constraint one level up, on the applications themselves. A UNS can serve as the transport of a KORS. The reverse has no meaning.

> The UNS answers "how do I bring my data together". KORS answers "how do I build production tools that never need to be integrated with one another".

### 10.2 Why a name

Every vendor already says "modular" and "open", and neither word commits to anything. A precise name with five criteria that a third party can check does commit: a system satisfies them or it does not.

Anyone may build a KORS and say so. This is intentional. The name is meant to become a shared reference, as "UNS" and "composable MES" have, not a mark defended in court.

## 11. Related work and what KORS owes to it

None of the individual properties required by KORS is new. Each has a name and a community that has carried it for years.

- **ISA-95 / IEC 62264** [IEC62264]: the vocabulary (Parts 2 and 4) and the publish/subscribe and request/response exchange model (Part 6, Messaging Service Model). B2MML is its XML implementation.
- **UNS (Unified Namespace)**, a term popularized by Walker Reynolds: event distribution across the whole enterprise, and the proof that a concept without a product can become a reference.
- **Asset Administration Shell and VDI/VDE 2193** [IEC63278] [VDI2193]: the idea of a common language made of a vocabulary, a message structure and interaction protocols.
- **Eclipse Arrowhead** [ARROWHEAD]: the minimal mandatory core through which autonomous systems find one another and cooperate.
- **PROSA and holonic architectures** [PROSA]: autonomous production entities that cooperate by messages.
- **Composable MES and Packaged Business Capabilities**: the decomposition of a production application into separately deployable capabilities, on top of a canonical data model.
- **Domain-Driven Design** [DDD]: the published language at the boundaries, the bounded context and the anti-corruption layer.
- **Self-Contained Systems and microservices** [SCS]: autonomous units with no shared database that prefer asynchronous communication.
- **MACH** [MACH]: an architecture label defined by a short list of principles rather than by a product.
- **O-PAS and i3X** [OPAS] [I3X]: the method, with normative requirements and verifiable conformance.
- **CloudEvents and AsyncAPI** [CLOUDEVENTS] [ASYNCAPI]: vendor-neutral ways to describe event envelopes and event-driven contracts.
- **Regulation (EU) 2023/2854 (Data Act)** [DATAACT]: the legal expression of the customer's right to access, export and move its data.

What KORS adds: the obligation to hold these properties together at the level of production applications, a name for that conjunction, and a definition that a third party can verify.

## 12. Security and safety considerations

This specification does not define security mechanisms. Opening contracts and data (C5) does not mean opening access: authentication, authorization per contract, network segmentation between IT and OT, and the protection of personal data remain the responsibility of each implementation and of the regulations that apply to it. A KORS is not a substitute for the safety functions of machines and control systems.

## 13. Open questions

- **Minimal core of the vocabulary**: which ISA-95 entities are mandatory? Proposal: equipment, material and lot, personnel, work order, process segment, non-conformance.
- **Use of ISA-95**: this specification cites the concepts and the parts of the standard without reproducing its text.
- **Conformance tooling**: checklist only, or an automatable test suite in the longer term.
- **Registry of extensions**: where named sector extensions (C2.5) are published and how their names are kept unique.
- **Governance**: see [GOVERNANCE.md](GOVERNANCE.md).

## 14. References

### Normative

- [RFC 2119] S. Bradner, *Key words for use in RFCs to Indicate Requirement Levels*, BCP 14, RFC 2119, March 1997. https://www.rfc-editor.org/rfc/rfc2119
- [RFC 8174] B. Leiba, *Ambiguity of Uppercase vs Lowercase in RFC 2119 Key Words*, BCP 14, RFC 8174, May 2017. https://www.rfc-editor.org/rfc/rfc8174

### Informative

- [IEC62264] IEC 62264 / ANSI/ISA-95, *Enterprise-control system integration*, Parts 1 to 6, latest editions.
- [IEC63278] IEC 63278, *Asset Administration Shell for industrial applications*.
- [VDI2193] VDI/VDE 2193, *Language for I4.0 components*, Parts 1 and 2.
- [ARROWHEAD] Eclipse Arrowhead. https://arrowhead.eu
- [PROSA] H. Van Brussel, J. Wyns, P. Valckenaers, L. Bongaerts, P. Peeters, *Reference architecture for holonic manufacturing systems: PROSA*, Computers in Industry 37(3), 1998.
- [DDD] E. Evans, *Domain-Driven Design: Tackling Complexity in the Heart of Software*, Addison-Wesley, 2003.
- [SCS] Self-Contained Systems. https://scs-architecture.org
- [MACH] MACH Alliance. https://machalliance.org
- [OPAS] The Open Group, *O-PAS Standard (Open Process Automation Standard)*. https://www.opengroup.org/forum/open-process-automation-forum
- [I3X] CESMII, *i3X 1.0*, 2026. https://i3x.dev
- [CLOUDEVENTS] CNCF, *CloudEvents*. https://cloudevents.io
- [ASYNCAPI] *AsyncAPI Specification*. https://www.asyncapi.com
- [DATAACT] Regulation (EU) 2023/2854 of the European Parliament and of the Council of 13 December 2023 (Data Act). https://eur-lex.europa.eu/eli/reg/2023/2854/oj

## 15. Attribution

© 2026 Sean Lamet and the KORS contributors. This document is licensed under the [Creative Commons Attribution 4.0 International License](LICENSE). To reuse it, credit:

> KORS, Kernel for Operations and Resources System. Sean Lamet / Dekrow, 2026. https://github.com/dekrow-corp/kors, CC BY 4.0.

If you modify the text, say so, and do not present the modified text as the KORS specification.
