---
title: Security considerations
---

## Keeping things under your control

For building trust, it’s a good strategy to not rely on third-parties. This will also help with other areas like Networking. Examples of this approach:

- Don’t include 3rd party tracking scripts. Make it easy to audit network traffic that your CLI is producing.
- Instead of pseudonymous S3 URL or GitHub Release page, use your own domain and CDN for distributing executables. Together with Code Signing and it’s easier to verify that downloaded executables are coming from you.
- Version everything you can.

## Elevation of rights

Careful with executing or shelling out based on user input.

## User input to access consoles

Your CLI might be running on a remote box.
Careful with destructive actions - could your CLI trigger a `rm -rf` with a specific user input?

## Less surprises

To be a good citizen, and make it easy for your users to deploy your CLI, have a document at hand describing:

- files your CLI is interacting with
  - Installation location(s)
  - Configuration files
  - System libraries or 3rd party packages your CLI is depending on. E.g. a `git` needs to be present on the system.
- networking requirements
  - Used protocols, ports or anything else that might need to be configured in user’s firewall
- Provide SBOM, signing keys

## SBOM, predictable builds and installs

### Predictability in live or auto-updating CLI
