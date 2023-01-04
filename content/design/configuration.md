---
title: Configuration
---

## Arguments and flags

## Environment

“Environment constants”

### Capitalization

Modern systems allow you to define envvars with any capitalization, but historically, it’s safer to expect those flags to be capital.

### Boolean flags

Either explicitly state that envvar being present, bearing any value, is an “On” state. Or if you want to have explicit switches, to modify behavior of your program follow the Robustness principle and accept different values for your boolean switches. Your “On” state could be any of these 1/TRUE/True/ENABLED/… and your “Off” state could be marked by 0/FALSE/False/DISABLED/…

### Some notes on parsing and security

If you want to get anything else than a String from an envvar, be vary of parsing them. Depending on your language, you might get unexpected or unwanted results e.g., when you are expecting a number to be set you might not be expecting `TIMEOUT=1000.00`, `TIMEOUT=1_000` or `TIMEOUT=1e3`. Similar applies to validating file paths.

## Configuration files

You may also offer a
For some tools it makes sense to offer a standalone

In ideal case, your configuration file format is easily evolvable and extendable.
It’s a good practice to mark a version of your configuration file.

Good formats to explore are JSON, XML or TOML. Or a simple line-by-line configuration.

All these differ in aspects like human-readable, language-independence, how easy are the to diff (should your config files be checked in with

### What about YAML?

YAML is a popular format for storing software configurations. In its basic form YAML is user friendly, easy
looks like a good
