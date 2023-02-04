---
title: Auto-updating CLI applications
keywords: installation, lifecycle, versioning, upgrade, auto-update
---

If you have security, business or other reasons to keep your CLI application up-to-date, you might want to consider implementing an auto-update mechanism.

<!--more-->

## Evolvable and smart clients

Especially if your CLI acts as a [thin API client](https://en.wikipedia.org/wiki/Thin_client), you could leverage evolvable API design patterns, like [Hypermedia API](https://en.wikipedia.org/wiki/HATEOAS) or [GraphQL](https://graphql.org), to make your CLI application evolve together with the API.

On a similar note, introducing a plugin or extension architecture, that can fetch plugins dynamically, could help with the evolution of your application.

## Nudging users to upgrade

As listed above, rolling out a mature auto-update mechanism is a complex journey. If your aim is to only keep as many users as possible on the latest version, **consider simpler solutions first**. For example, you could add a message to the CLI output, that would nudge users to upgrade to the latest version.

Or, if your CLI is calling your web API, with [a well-formed User-Agent]({{< relref "cli-networking#setting-an-identifier-header" >}}), you can detect users running outdated versions, and user you web application or email messaging to let them know they should upgrade.
