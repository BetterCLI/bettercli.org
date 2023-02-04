---
title: Self-executing installation scripts
weight: 2
---

Installer scripts for CLI applications often piped from the internet with _curl pipe bash_ are controversial. They offer ease of use but face security and reproducibility issues.

<!--more-->

There are [many projects providing web installers for CLI applications](https://kubikpixel.github.io/pipeinstall/). To the user, they often look like this:

```text
curl https://multipu.sh/install.sh | sh
```

This elegant one-liner is all the user needs.

Running this command will connect to the URL `https://multipu.sh/install.sh`, downloads its contents and pipes and executes it with `sh`. **What happens on your machine is then fully in the control of the script that gets downloaded.** At this point, the user assumes you will do your best. There are no forced steps or checks, and no lifecycle expectations you would get with package managers.

**This is the most flexible installation method for your CLI.** Self-executing installation scripts can be made available for multiple platforms, different setups and contexts. This single installation method can satisfy users across a broad range of platforms and setups. It's as easy and clear to run locally, as it's to integrate into a CI pipeline or a Dockerfile.

And the sky is the limit when thinking about functionality - do you want to provide an interactive setup wizard for first-time users? Auto-upgrade functionality? And **this flexibility is also open for the users**, they can download and modify the installation script to match their needs.

This is a contrast to the more traditional approach when distributing your code with a package manager.

As much as this approach beats DIY installers, it lacks some benefits of OS-specific installers and managers.

## Security

Especially with [ways to detect when the user is piping curl to bash](https://www.idontplaydarts.com/2016/04/detecting-curl-pipe-bash-server-side/), it's easy to imagine an attacker utilizing this vector. Users are taking a substantial risk when running your installer. For such reasons, CLI authors should strive to make these installers as safe and predictable as possible.

## Usability

The usability of the installer is a bit of a paradox. It's often promoted as an easy way for developers to install the tool locally. It's true, it will get a developer with any experience level and on _any_ machine ready to use your tool. The installer is ideally self-contained, it could be dropped into any environment or script and it will get the job done.

But other parts of the [CLI lifecycle]({{< relref "cli-application-lifecycle" >}}) are not as easy.

Users have no way to easily discover what tool they installed. Compared to tools provided by package managers like `apt list --installed` or `brew list`. Because the nature of install scripts being so free form, the application could be installed in multiple locations

On the same note, they have no good way to check if there is an updated version and install it. Again, this forces CLI authors to roll out their own solutions, like [the update-notifier npm package](https://www.npmjs.com/package/update-notifier) for checking for updates and suggesting to users how to upgrade.

![npm update-notifier screenshot](npm-update-notifier-screenshot.png 'Example output of the update-notifier package')

And if they want to upgrade, do they rerun the installation script? Or will you provide a _custom_ `upgrade` command? This leads to [an even longer tail of old versions you will see in analytics]({{< relref "cli-application-lifecycle#why-are-old-versions-a-problem" >}}). Unless you build an auto-update mechanism yourself.

## Building a curl pipe bash installer

{{% important-list %}}

1. You can’t declare dependencies. Which is a thing you maybe don’t want to do in the first place, when thinking about Securing your CLI.

1. Provide a way to pass configuration options. For example to provide a custom installation location, to skip an installation check

1. If you host your scripts, you control the pipeline. Use a trusted domain you own. Not rely on 3rd parties to host, distribute or install your tool could be a benefit.

1. When documenting the command users should run, include flags for `curl` or `wget`. For example `-L` to follow redirects. This is important, as you might want to change the URL of your script in the future. See more examples below.

1. Provide a versioned links, when making changes to the install script. Or options to pass

1. Provide alternative installation option as there are situations where this installation is not feasible.

1. You don't know anything about the environment your command will run in. Be very defensive, check all assumptions and provide helpful error messages.

1. If you are downloading binaries, use the same domain as the installation script.

{{% /important-list %}}

## Support CLI lifecycle

There are a few things in mind when creating your installer.

- There could already be an existing version of your CLI.
  - It could be newer (Prerelease? Development version?) than your script
  - Or it could be very old

### Reproducibility: Specifying CLI version

### Keep the installer body in a function

This method prevents a commonly cited pitfall of these installation scripts.

### Updating the installer

From the nature of these scripts, they don’t have to change too often. However, once in a while, you need to support a new system, architecture or setup. Once these scripts are sufficiently complex, with special cases for specific systems and configurations, they are not easy to test. Making changes to such scripts is then a nerve-racking exercise.

Users are executing your script on their machines and the danger of fatal errors is real. To make matters worse, instrumenting any kind of [error reporting]({{< relref "debugging" >}}) or [analytics]({{< relref "collecting-analytics" >}}) in these scripts is a challenge.

## Read more

- [The dangers of curl | bash](https://www.seancassidy.me/dont-pipe-to-your-shell.html)
- [webinstall.dev](https://webinstall.dev/)
