---
title: CLI distribution and installation
---

How users install your CLI is a very important consideration. It should reflect preferences within your user-base and it influences your options and choices regarding things like build process, release cadence, update mechanisms and more.

On this page, let’s look at some of the options. There are many aspects to consider and a way too many options. It’s a fair strategy to start simple and listen to your users. However, choosing an installation method also influences how easy or complicated is it to install a new version or uninstall your application.

<!--
CLI app’s code

- users building their own
  - Can your analytics handle this? Will it include a correct version number? Or will it make a mess in your reporting?
- you are releasing it
- 3rd parties caching it: repository mirrors, caching proxies, in some cases archiving or download servers and more
- users caching it in their Artifact managers like Nexus or Artifactory
- Application is on the developer machine, it could be installed by a installer, package manager or manually. For all users or just specific ones? It could be on a network drive or a container, available for other users.
- =\> user machine
-->

## Installation lifecycle

Installation and distribution is only one part of the story. There are many aspects of an installation method to consider:

- Is it convenient?
- Is it secure?
- Can you install a specific version? E.g., an older one or a prerelease?
- Is it working cross-platform?
- Can you mark a version as deprecated to push users to upgrade?
- Can you as a maintainer see a number of downloads or activations? Per version or platform?

Taking these into accounts, your CLI app should ideally be able to answer:

- How to install the CLI app?
- How to keep the CLI app up to date?
- Are there dependencies that needs to be installed? And kept up to date?
- What operating systems and versions are supported?
- How to remove/uninstall the CLI app **and all its files ** like settings or caches?

The best scenario is when:

- users can install on every operating system
- installing, upgrading, downgrading & removing is simple and secure
- installation method gives you some security guarantees like certificate and sha verification
- maintainer may deprecate older versions, signalling users they should upgrade
- maintainer gets at least basic analytics on installations or activations

## Distribution channels and installation methods

This section looks at possible ways how to distribute your CLI app. You don’t have to chose just one. Common pattern is to support multiple installation methods for your CLI.

### Source code and build instructions

One option is to provide an open-source tool with its build instructions. This may look like a project repository on GitHub with a `make` file for local builds. Readme of such app would asks users to download a copy of project’s source code locally, ensure all dependencies and requirements are met. And run the executable build locally, resulting in their own copy of the CLI app. Then they need to verify build worked and make sure that the CLI app is available in the `PATH`.

There are a few downsides for the endusers:

- It requires a fairly technical user base and a repeatable and locally executable build system. If you plan to support your CLI app, invest into the build tooling to make troubleshooting easier further down the line.
- Versioning, updating or removing the locally installed version is now a manual and error-prone task.

### Unmanaged executables

In this scenario, CLI app author is providing a prebuilt executables. This approach can take many different directions and is usually combined with other distribution methods.

This means that users can download pre-built executables on their own. Common example of this are releases made available on GitHub Releases page. This often includes executables built for specific operating systems

This also means that users are required to keep their downloaded version updated[^1] and take care of any filesystem operations.

Specific category here are the Self-executing installation scripts.

### Language-specific tooling

This is probably a good choice when you are building a language-specific tool.

There are package managers that can take care of distribution, installation management and execution of your CLI app. npm is a prime example of such tooling. By running `$ npm publish`, your locally created manifest `package.json` and related `.js` files are uploaded and hosted on npmjs.org. Resulting package is immediately available for anyone to install and use. Installation is quick[^2] and secure[^3].

Another possible benefit here is that your CLI app can be setup to also work as a language library. Allowing users to integrate it into their applications. A good example here would be a JavaScript library, with its own executable (`bin`) entrypoint. Allowing users to run the CLI app from terminal. But also exposing its language APIs to be used as a module - for example with a `import * as multipush from 'multipush'`.

Relying on language-specific tooling is usually rather convenient. These package managers are usually mature. Often taking care of seamless update process, cross-platform compatibility and security concerns. However, it requires users to install and update a language-stack they might not be familiar with in order to use your CLI app.

#### JavaScript/TypeScript: npm, yarn & npx

#### Python: pypi

#### Ruby: gem

### OS-specific tooling

There are also many tools aimed at distributing executables & CLI apps focusing on specific operating systems. These are usually language-agnostic and relying on provided executables.

#### Linux

#### macOS

- homebrew
- macports [https://www.macports.org/](https://www.macports.org/)

#### Windows

- scoop
- chocolatey

### Domain-specific tooling and „app stores“

There are situations where it makes sense to make your CLI app available on specialised platforms. This could include integrations and marketplaces like [GitHub Marketplace for Actions](https://github.com/marketplace?type=actions), VS Code Extensions listing, CircleCI Orbs, [Jenkins Plugins](https://plugins.jenkins.io/) and many many more.

This often comes with a requirement to _wrap_ your CLI to make it compatible with such platforms and stores. Or to provide additional functionality, like making output visible in the integration UI.

#### Application plugins

CLI apps are used as a building blocks of other applications. Your CLI might be bundled e.g., as part of another CLI app, as a dependency of a desktop app or an IDE plugin or even as part of an operating system.

#### Docker images

One more specific distribution mechanism is a Docker image. It’s a rather portable and convenient mechanism to distribute your CLI app. Even better when the Docker image itself is benefiting from one of the distribution channels you’ve setup - for example a Dockerfile is using a `pypi` installer to install and setup your CLI app, just as your users would.

You have an option to provide just a Dockerfile, that users need to build themselves. Or a prebuilt Docker container that can be fetched and executed.

Docker containers are fairly ubiquitous and easy to integrate in some workflows like CI pipelines or cloud workloads. Heavily rely on tags and good versioning practices.

## Distribution considerations

- code signing
- Antivirus software
- SBOMs, shas and verifications

## Uninstallation

[^1]: Unless your app provides self-update
[^2]: Because only your compressed .js files are downloaded. The Node.js runtime needed for your package is already installed on user’s machine.
[^3]: npm employs a few techniques to ensure that requested packages are not tampered with after release or in transport.
