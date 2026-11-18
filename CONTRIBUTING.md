# Contributing

Proposals go through an issue (to discuss) or a pull request (to change the text). Changes to the requirements start with an issue; see [GOVERNANCE.md](GOVERNANCE.md).

Participation is subject to the [code of conduct](CODE_OF_CONDUCT.md).

## Developer Certificate of Origin (DCO)

Every commit MUST carry a `Signed-off-by` line with your name and e-mail address:

```
git commit -s -m "Clarify criterion C3.4"
```

By signing off, you certify the [Developer Certificate of Origin 1.1](https://developercertificate.org/): you have the right to submit the contribution, and you agree that it is published under the CC BY 4.0 license with the rest of the repository. You keep the copyright in your contribution.

Pull requests without `Signed-off-by` are not merged. A check runs on every pull request.

## Writing rules

- One requirement per line, numbered (C1.1, C1.2...), using the words MUST, MUST NOT, SHOULD, MAY.
- New requirements take the next free number under their criterion. Numbers are never reused.
- No requirement may impose a technology, a product or a vendor.
- Cite standards (ISA-95, IEC 62264...) by name and part, without reproducing their text.
- Every change to a requirement is recorded in the CHANGELOG.
- The English text is normative. If you change SPEC.md, update the translations in the same pull request, or say in the pull request that they need updating.
