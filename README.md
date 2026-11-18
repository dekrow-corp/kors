# KORS

**Kernel for Operations and Resources System.** An architecture concept for production software. Not a technology, not a product.

The UNS answers "how do I bring my data together". KORS answers "how do I build production tools that never need to be integrated with one another".

The usual reflex in a plant is to buy software one piece at a time, then pay someone to make the pieces talk. It works, more or less, until the next upgrade. KORS takes the problem the other way round: instead of connecting applications after the fact, services are built from the start on a shared vocabulary and shared events.

## The five criteria

A system may call itself KORS if it satisfies all five. Not four out of five.

1. **Event-based communication**: every change of business state is published by the service that owns it, under a versioned contract.
2. **Common vocabulary at the boundaries**: exchanges use a common vocabulary derived from ISA-95; each service remains free in its internal model.
3. **Autonomy of each service**: each one is deployed, evolves and fails on its own.
4. **Modular adoption**: each service is worth something alone, and more with the others.
5. **Open data**: documented contracts, full export, hosting chosen by the customer.

The full definition, the checklist and the examples are in [SPEC.md](SPEC.md).

## What KORS is not

- **A technology.** NATS, Kafka, MQTT or Dapr can be used to build one. They are interchangeable.
- **A middleware.** KORS describes how services are built to talk to one another, not how to connect existing software after the fact.
- **A trademark.** Anyone may build a KORS and say so, provided the five criteria are met.

## In this repository

| File | Content |
|---|---|
| [SPEC.md](SPEC.md) | The specification: definitions, the five criteria, conformance, checklist, related work |
| [CONFORMANCE.md](CONFORMANCE.md) | How to state that a system is KORS, with a statement template |
| [CHANGELOG.md](CHANGELOG.md) | Every change to the specification, version by version |
| [GOVERNANCE.md](GOVERNANCE.md) | Who maintains the specification, how it changes, how the name is used |
| [CONTRIBUTING.md](CONTRIBUTING.md) | How to propose a change |
| [AUTHORS.md](AUTHORS.md) | Authors and contributors |
| [CITATION.cff](CITATION.cff) | Citation metadata |
| [translations/](translations/) | Non-normative translations (French) |

## Origin

KORS was conceived and named by Sean Lamet in 2026. This repository is its first publication and its reference: version 0.1 was released on 18 November 2026.

It is published by [Dekrow](https://dekrow.com) ([dekrow-corp](https://github.com/dekrow-corp) on GitHub), which is its first user, not its owner.

KORS does not claim to have invented its building blocks. Events, a shared ISA-95 vocabulary, autonomous services, modular adoption and open data each have a history, acknowledged in [section 11 of the specification](SPEC.md#11-related-work-and-what-kors-owes-to-it). What originates here is the name, the definition of KORS as the conjunction of these five properties at the level of production applications, and the criteria that make it verifiable.

## License and citation

The texts in this repository are © 2026 Sean Lamet and the KORS contributors, licensed under the [Creative Commons Attribution 4.0 International License](LICENSE) (CC BY 4.0). You may copy, translate and adapt them, including for commercial purposes, provided you credit the origin, link to the license and indicate any changes:

> KORS, Kernel for Operations and Resources System. Sean Lamet / Dekrow, 2026. https://github.com/dekrow-corp/kors, CC BY 4.0.

To cite a precise version, use [CITATION.cff](CITATION.cff) or the DOI of the corresponding release.

What the license covers: the text. The ideas described here are free for anyone to implement, with no permission, fee or notice required.

## Using the name

KORS is not a registered trademark and is not meant to become one. Two requests, in the interest of everyone who relies on the word:

- Say that a system is KORS only if it satisfies the five criteria, and say which version of the specification you mean. See [CONFORMANCE.md](CONFORMANCE.md).
- Do not present a modified version of these texts as the KORS specification, and do not imply endorsement by the authors.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md), [GOVERNANCE.md](GOVERNANCE.md) and the [code of conduct](CODE_OF_CONDUCT.md).

## Contact

Questions about the specification go to the [issue tracker](https://github.com/dekrow-corp/kors/issues). For anything else: contact@dekrow.com.
