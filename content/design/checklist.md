---
title: CLI Design Checklist
---

This is a checklist of things to consider when designing a CLI app. It is not a comprehensive list, but rather a collection of things that are often overlooked. It is a living document, so feel free to suggest additions or changes.

## Basics

- [ ] Provide [CLI documentation and help page]({{< relref "cli-help-page" >}})

## help

- [ ] provide manpage
- [ ] Be available in helpers
- [ ] Have docs searchable (google or internal) so that users can link to it
- [ ] Have troubleshooting

## output

- [ ] [Document exit codes]({{< relref "exit-codes" >}})

## installation

- [ ] shas
- [ ] Shas with gpg key
- [ ] Sbom
- [ ] Code signing

## macos specific (N/A)

- [ ] homebrew
- [ ] Codesigned
-
