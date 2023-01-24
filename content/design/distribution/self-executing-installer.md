---
title: Self-executing installation scripts
---

Installer scripts for CLI applications often piped from the internet with _curl pipe bash_ are controversial. Combining ease of use with security and reproducibility is a challenge.

<!--more-->

```bash
curl https://multipu.sh/install.sh | sh
```

A single-line, cross-platform installer solution. with a lot

Provide alternative installation option as there are situations where this installation is not feasible.

This approach provides plenty of benefits:

- A one-liner, especially one that is not depending on tools beyond standard (even Windows comes with it!) `curl` and `sh` makes onboarding users or integrating into new system very easy.
- If you host your scripts, you control the pipeline. Not rely on 3rd parties to host, distribute or install your tool could be a benefit.

As much as this approach beats DIY installers, it lacks some benefits of OS-specific installers and managers.

- You can’t declare dependencies. Which is a thing you maybe don’t want to do in the first place, when thinking about Securing your CLI.

There are a few things to recommend here, beyond the body of the actual install script:

- Use a trusted domain you own.
- Provide a versioned

## Usability

## Notes

From the nature of these scripts, they don’t have to change too often. However, once in a while you need to support a new system, architecture or setup. Once these scripts are sufficiently big, with special cases for specific systems and configurations, they are not easy to test. Making changes to such scripts is then a nerve-racking exercise.

Users are executing your script on their machines and the danger of fatal errors is real.

## Support CLI lifecycle

There are a few things in mind when creating your installer.

- There could already be an existing version of your CLI.
  - It could be newer (Prerelease? Development version?) than your script
  - Or it could be very old

### Reproducibility: Specifying CLI version

CLI

## Security

## Building a curl pipe bash installer

### Keep installer body in a function

This method prevents a commonly cited pitfal of these installation scripts.
