# Governance

## Maintainers

The specification is maintained by [Dekrow](https://dekrow.com). The current maintainers are listed in [AUTHORS.md](AUTHORS.md).

## Decisions

- Changes to the text with no effect on the requirements (wording, examples, translation) are merged by a maintainer after review.
- Changes to the requirements (addition, removal, moving from SHOULD to MUST...) are discussed in a public issue and decided by consensus of the maintainers.
- The five criteria only change with a new major version.
- While the version is 0.x, the detailed requirements under each criterion may change in any minor version. Each change is recorded in the [CHANGELOG](CHANGELOG.md).

## Versions

- Versions follow the `major.minor` scheme (0.1, 0.2, 1.0...).
- Each version is a signed Git tag, published as a GitHub release and archived with a DOI.
- A published version is never edited. Corrections go into the next version.
- Requirement identifiers (C1.1, C3.4...) are stable across versions and never reused.

## Canonical text and translations

- The canonical text is the English text in the repository https://github.com/dekrow-corp/kors, at the tag of the version concerned.
- Translations live in [translations/](translations/). They are not normative: where a translation and the English text differ, the English text prevails.

## Use of the name

KORS is not a registered trademark. The name is free to use to describe a system that satisfies the five criteria, as set out in [CONFORMANCE.md](CONFORMANCE.md). The maintainers do not certify systems and do not endorse products. A text that modifies the requirements is not the KORS specification and should not be presented as such.

## Evolution

In time, the governance is meant to open to other organizations that build KORS systems.
