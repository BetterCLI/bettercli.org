---
title: Versioning CLI applications
keywords: installation, lifecycle, versioning
---

Versioning your CLI application is an important part of your CLI design. And planning ahead when deciding on the versioning scheme could help you avoid painful troubles in the future.

<!--more-->

## major.minor.patch aka Semantic Versioning

Many of the distributions mentioned in [CLI distribution section]({{< relref "distribution" >}}) expect or even require your versioning scheme to be compatible with [Semantic Versioning](https://semver.org/#spec-item-10) _(SemVer)_. Unless you have specific needs, that can’t even be supported by [SemVer’s Build metadata section](https://semver.org/#spec-item-10), choosing SemVer is the most _compatible_ option.

When deciding on the versioning, you can consider few more aspects and needs specific to your application:

- Do you want (or can you even?) support installing a specific major version or version ranges? Will users be able to install `multipush@v1`, `multipush@v2` or `multipush@<1.1.0`? Or will they always have to specify full version identifier like `multipush@1.0.0+2023-01-01`? If we allow version ranges, then extra care needs to be taken to not introduce a regression?
- Will only the latest version be supported? Then it’s a good idea to indicate to users _how old_ their version is - possibly by including a build date to the version number? `1.0.0+2023-01-01`

### Semver build metadata

SemVer allows for a build metadata section, which is a set of identifiers separated by dots. This is a good place to include a build date, or a commit hash, or a build number.

```text
multipush v1.0.0+2023-01-01
multipush v1.0.0+e83c516
```
