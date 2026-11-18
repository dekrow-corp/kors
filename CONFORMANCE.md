# Stating KORS conformance

Conformance to KORS is self-declared ([SPEC.md, section 7](SPEC.md#7-conformance)). There is no certification body, no fee and no mark. What gives a claim its value is that anyone can check it against the evidence.

## Rules for a claim

A conformance claim:

1. names the version of the specification ("KORS 0.1");
2. states its scope: which services, in which deployment;
3. points to the evidence listed in the [checklist](SPEC.md#8-checklist);
4. lists every SHOULD requirement that is not satisfied, with the reason;
5. does not state or imply verification or endorsement by the authors of the specification.

There is no partial conformance. A system that satisfies four criteria out of five is not KORS, and "KORS-ready", "KORS-compatible" or "KORS-inspired" are not terms defined by the specification.

A single product can be described as "designed for KORS 0.1" if it satisfies every requirement that applies to an individual service. Conformance itself is a property of a deployed system.

## Statement template

```markdown
# KORS conformance statement

- Specification version: KORS 0.1
- System: <name of the system or deployment>
- Declared by: <organization>, <date>
- Services in scope: <list, with vendor for each>
- External systems and their adapters: <list>

## Evidence

| Criterion | Evidence | Where to consult it |
|---|---|---|
| C1 Event-based communication | <list of event contracts, isolation test> | <link> |
| C2 Common vocabulary | <published vocabulary, deviations from ISA-95> | <link> |
| C3 Autonomy | <per-service upgrade procedure, contract versioning policy> | <link> |
| C4 Modular adoption | <service deployed alone, service added by subscription> | <link> |
| C5 Open data | <export procedure, API documentation, hosting guide, exit terms> | <link> |

## Deviations from SHOULD requirements

| Requirement | Deviation | Reason |
|---|---|---|
| <e.g. C2.2> | <description> | <reason> |
```

## Listing a statement

Published statements can be proposed for listing below by pull request. A listing records that a statement exists; it is not a verification.

| System | Declared by | Specification version | Statement |
|---|---|---|---|
| *None yet* | | | |
