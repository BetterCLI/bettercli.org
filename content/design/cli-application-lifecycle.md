---
title: CLI application lifecycle
draft: true
---

When making decisions in an existing CLI application, or design a new one, you should consider specifics parts of its lifecycle. There is a stark contrast to consider when you are coming from a background of Web Apps or Services.

Once you release an app, there is no taking it back.

Making any kind of change will likely affect some of its users.

Once it’s released, it will be run in a wildest possible setups you can’t imagine. It will be used for things it was not designed to do. It will run on machines that were never tested or even considered.

CLI app’s code

- users building their own
  - Can your analytics handle this? Will it include a correct version number? Or will it make a mess in your reporting?
- you are releasing it
- 3rd parties caching it: repository mirrors, caching proxies, in some cases archiving or download servers and more
- users caching it in their Artifact managers like Nexus or Artifactory
- Application is on the developer machine, it could be installed by a installer, package manager or manually. For all users or just specific ones? It could be on a network drive or a container, available for other users.
- =\> user machine

It’s a bad practice to purposedly include a code for bricking/disabling your Application.

Once the CLI application makes it to the destination machine:

- it could stay there for years. If it fulfils its intended purpose, apart from security patches, there is little reason to upgrade.

Upgrading

- Keep settings untouched, or talk to the user
- rollbacks
  - Handling previous secrets and options
    - For example, when changing a structure of a config file, you will probably include a code to handle or upgrade the old structure. But can your code handle and inform users they are using a structure that’s too new?
- If you implemented auto update, how good is the experience for users deciding to stick to an older version?
- removal
  - clear caches
  - clear config store
