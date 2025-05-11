---
title: Configuration
---

Your application needs to be _configured_. One way of looking at CLI configuration is to distinguis between _global_ and _local_ configuration. Global configuration is the configuration that is shared across all invocations of your CLI. Local configuration is the configuration that is specific to a single invocation of your CLI.

<!--more-->

## Global and local configuration

Global configuration is the configuration that is shared across all invocations of your CLI. Local configuration is the configuration that is specific to a single invocation of your CLI. Both can have many shapes and forms. Often, they may interact with each other, e.g., by overriding each other when configurations overlap.

## Configuration methods

- Arguments and flags
- Environment variables
- Configuration files
- Remote configuration

## Arguments and flags

## Environment

“Environment constants”

### Capitalization

Modern systems allow you to define envvars with any capitalization, but historically, it’s safer to expect those flags to be capital.

### Boolean flags

Either explicitly state that envvar being present, bearing any value, is an “On” state. Or if you want to have explicit switches, to modify behavior of your program follow the Robustness principle and accept different values for your boolean switches. Your “On” state could be any of these 1/TRUE/True/ENABLED/… and your “Off” state could be marked by 0/FALSE/False/DISABLED/…

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

### Using programming language as configuration

An interesting approach could be to **use a programming language as configuration**. This is a good approach if you want to have a lot of flexibility in your configuration.

This approach might also be a good fit if you operate in a specific programming language ecosystem. E.g., having an npm CLI programs accepting JavaScript `.js` files as configuration.

You can use a programming language to define your configuration and then use that configuration in your application. Candidates for such scripting language, that are also fairly straightforward to embed, are Python, JavaScript, or even Lua.

It's worth noting that there are even programming languages that are designed to be used as configuration languages. These languages are often designed to be easy to read and write, and they often have features that make them well-suited for configuration tasks. Examples of such languages include [Jsonnet](https://jsonnet.org), [HCL](https://github.com/hashicorp/hcl) (HashiCorp Configuration Language) and [JSON5](https://json5.org).

Using such approach puts a lot of expectations on high quality documentation including configuration examples. This also raises a bar on error messages: are you able to pinpoint a syntax error? An invalid use of a language feature? Testing and versioning of these configurations is also suddenly _much more_ complex.

This might make sense for very stable APIs.

## Parsing and security

If your application wants to retrieve anything else than a _String_ from a configuration, be vary of parsing them. Treat them as an unexpected input and be ready to fail safely. Depending on your language, you might get unexpected or unwanted results e.g., when you are expecting a number to be set you might not be expecting `TIMEOUT=1000.00`, `TIMEOUT=1_000`, `TIMEOUT=-1000`, or `TIMEOUT=1e3`. Similar applies to validating file paths, URLs and similar.
