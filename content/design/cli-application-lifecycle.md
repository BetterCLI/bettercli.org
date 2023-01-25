---
title: CLI application lifecycle
---

When making decisions in an existing CLI application, or designing a new one, you should **consider specific parts of the CLI application's lifecycle**. It differs from a web application or a service, where you can easily update the code and the users will get the new version the next time they visit the website. CLI applications are much closer to desktop applications.

<!--more-->

## What is a CLI application lifecycle?

Capturing what happens to a CLI application, once it's written is a complex task. It's not just about the code, but also about the distribution, installation, and the way the application is used. It's also about **the way the application is upgraded, downgraded, and uninstalled**.

In this simplified, but already too complicated diagram below, you can see two major parts: **Distribution** and **Lifecycle**. This page will focus on the **Lifecycle** part of the diagram: **what is happening with the CLI application once it's installed on the user's machine**. A few things you want to keep in mind:

- The machine belongs to the user, your application is a guest.
- Users have **good** reasons to...
  - ... stay on a specific version of the CLI, without upgrading. Even for _years_ and possibly longer.
  - ... configure and use the application in ways you never imagined. Or recommended.
  - ... switch between versions and installation methods back and forth. Or even run multiple versions installed with different package managers on the same machine.

Management of the installed application is in the hands of the user. It's not uncommon that **CLI applications stay unchanged on a user machine for years**. But their options could be dictated by the way you designed your application.

```mermaid
stateDiagram-v2
    code: CLI code
    officialBuild: Official build
    build: Users building their own copy
    release: CLI released to official channels
    caching: Repository mirrors (Artifactory), caching proxies, archiving servers
    artifact: CLI executable is made available
    user_machine: User machine
    uninstall: Cleanup after itself?
    upgrade: Backwards compatibility?
    downgrade: Handling new options and settings?

    state Distribution {
      code --> officialBuild
      code --> build
      officialBuild --> release
      release --> caching : 3rd parties caching your CLI
      build --> artifact
      release --> artifact
      caching --> artifact
      artifact --> [*]
    }

      Distribution --> Lifecycle : Manual installation
      Distribution --> Lifecycle : Package manager
      Distribution --> Lifecycle : Provisioned by administrator
      Distribution --> Lifecycle : Part of a Docker image or a Linux distro

    state Lifecycle {
      [*] --> user_machine
      user_machine --> user_machine : Automatic updates?
      %% TODO: this loop should be at least mentioned: user_machine --> user_machine : Is it running continuously? Or as a one-off command?
      user_machine --> downgrade : User decided to downgrade the CLI
      user_machine --> upgrade : User decided to upgrade the CLI
      user_machine --> uninstall : User decided to uninstall the CLI
    }
```

## Installing CLI application

The installation method used will determine user options. For example, if a user used a package manager installation path, then lifecycle parts like **upgrading, downgrading and uninstalling are handled by the package manager**. At the same time, auto-upgrading, unless specifically designed to work well with a package manager, could be problematic.

{{% callout %}}

Read more about the [CLI Distribution aspect on a dedicated page]({{< relref "distribution" >}}).

{{% /callout %}}

## Auto-update mechanism

**Auto-updating software deserves a dedicated page.** It's right on the border of Distribution and Lifecycle. From the lifecycle angle, it's important to consider:

- If you implemented auto-update, how good is the **experience for users deciding to stay on a specific version**?
- Do you **respect different installation methods**? E.g. if users are installing your application from the local mirror or Artifactory, will you fetch the new version from there? Or will you try to use your global store?
- Do you have different policies for e.g. security patches vs. major releases with breaking changes?

## Upgrading CLI version

{{% callout %}}

See the [Versioning]({{< relref "versioning" >}}) page for additional context.

{{% /callout %}}

A new version of the CLI should handle previous secrets and options. Are you supporting _very_ old versions? You should [version your config files and secrets]({{< relref "configuration" >}}), and handle the upgrade process. Will you update the configuration files once they were loaded or validated for the new version as well?

### Why are old versions a problem?

For CLI authors, especially if you are working on a commercial product, keeping the [long tail](https://en.wikipedia.org/wiki/Long_tail) of old versions shorter is preferable. This will greatly help with rolling out security patches, new functionality or even auditing.

<!--
Users might not care too much. If the tool gets the job done, there isn't an incentive to upgrade.

Traditionally, this wasn't a big issue. As a user, you want to be reasonably recent for bug fixes, and compatibility patches.
In recent decade or so security -->

## Downgrading CLI version

Users may also downgrade to a previous version - for compatibility reasons or because the new version introduced regressions. Or they just tested a prerelease version of your application. **How does your CLI behave in cases where it encounters an unknown (future) configuration version?** [Be explicit with your error messages.]({{< relref "messaging" >}})

## Uninstalling CLI

When you use configuration files and caches, do you clean them up when the user uninstalls your application? Can you run a clean or post-installation hook for all different installation methods? If not, do you have different cleanup behaviors for different users?

## Evolvable and smart clients

Maybe application updates its "plugins" or "extensions" on the fly?

Is it like with NASA updating satellites in space?

For API CLIs, you could rely on API and concepts like Hypermedia or potentionally even a GraphQL to make CLIs evolvable
