---
title: Better CLI
summary: Guide for developers, product managers, technical writers, UX designers and anyone else designing, building or maintaining a Command Line Interface application.
---

{{% columns %}} <!-- begin columns block -->

## Why CLIs?

CLI applications live in an interesting space, where human-computer interaction naturally blends with automation and programming.

Command-line interfaces are often defined as _just that_: text-based interfaces or APIs. This makes some sense, as **CLI are well useable for humans to input commands and for machine-to-machine automation and scripting**. It could be a utilitarian script performing a single action. Or a major application meant to run continuously as a service.

The textual interface crosses architectural paradigms, programming languages, and operating systems. **CLI applications are a rather specific area.** In many ways close to GUI/desktop applications, with [a specific lifecycle]({{< relref "cli-application-lifecycle" >}}). In other ways, CLI applications greatly benefit from being aware of and sometimes implementing current trends in the cloud and DevOps.

CLI application could be a massive ecosystem on its own. With hundreds of commands and options, complex integrations or 3rd party plugins. Or it could be a companion application to another product or a single-purpose utility.

Command-line interfaces are with us for decades. They accumulated layers of practices and rules but also evolved together with the ecosystems built around them.

<---> <!-- separator, between columns -->

## Introduction

This document should act as a best practices showcase and reference for anyone working on CLI applications or tooling.

---

There are 3 main sections, all born out of frustration when working on CLIs:

1. [Designing a CLI]({{< relref "design" >}}): outlining common topics, patterns and pitfalls.
1. [How-to guides]({{< relref "how-to" >}}): a collection of tips and tricks for tackling specific problems.
1. [Glossary of CLI-related terms and concepts]({{< relref "glossary" >}})

{{% /columns %}}

<!-- TODO: This documentation should be available as a CLI. Including auxiliary tooling it will try to propose -->
