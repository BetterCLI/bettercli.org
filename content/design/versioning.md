---
title: Versioning CLI applications
---

Versioning your CLI application is an important part of your CLI design.

## Semantic Versioning might be the best choice

Many of the distributions mentioned in “CLI distributions” expect or even require your versioning scheme to be compatible with [Semantic Versioning](https://semver.org/#spec-item-10) (SemVer). Unless you have specific needs, that can’t even be supported by [SemVer’s Build metadata section](https://semver.org/#spec-item-10), choosing SemVer is the safest and most compatible option.

When deciding on the versioning, you can consider few more

- Do we want (or can?) support installing a specific major version or version ranges? Will users be able to install `multipush@v1`, `multipush@v2` or `multipush@<1.1.0`? Or will they always have to specify full version identifier like `multipush@1.0.0+2023-01-01`? If we allow version ranges, then extra care needs to be taken to not introduce a regression?
- Will only the latest version be supported? Then it’s a good idea to indicate to users _how old_ their version is - possibly by including a build date to the version number? `1.0.0+2023-01-01`
