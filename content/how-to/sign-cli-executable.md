---
title: How to code sign CLI executables?
---

As a sign of your CLI’s maturity, you can to provide a way for users to verify that the CLI executable is coming from you.

Added benefit is that users, especially macOS users, will be spared a popup dialog that might show up when running executables downloaded from the internet. This could also help with false-positives detections with antivirus software and more.

Setting up code signing is not yet free, but not overly complicated. This process, with the exception of SigStore, is generally not aimed towards individual developers. You will need an entity ideally a company, that needs to get verified by the Root authority.

## Combining with checksums, SBOMs and other tools

Code signing your executable is not the only way to establish trust and make users feel more secure.

## Code signing Windows .exe executables

As with any certifiacate, make sure to setup a reminder to renew the certificate and set up a test that can verify the expiration date, so you won’t sign and release an executable with expired certificate. It could be troublesome, especially in the context of “you can’t take it back” in the CLI Lifecycle.

## Code signing macOS executables

There are a few things to keep in mind, when you

- You will need to sign up for an Apple Developer Account. This will cost you $99/year.
- Code signing needs to happen on a machine running latest macOS. You will need a macOS (VM) in your release pipeline at some point.
- Similar to the Windows Code Signing, Apple’s Code signing, or better Notarization, relies on Apple’s central server, that could be unreliable and could block or fail your release process.

## Notes on code signing executables for Linux

There is an emerging standard and tooling called SigStore.
[http://sigstore.dev](http://sigstore.dev)
