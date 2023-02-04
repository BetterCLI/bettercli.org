---
title: Debugging
---

Gaining visibility into CLI failures and errors is an important task both for CLI developers and its users. CLI authors want to get visibility into common issues and regressions - for this you want to look into enabling debuggin information combined with [Usage Analytics]({{< relref "collecting-analytics" >}}). While users, in case they encounter an error, probably want to accomplish a task and want to understand what went wrong.

<!--more-->

## Local debug output

First and the most common step is providing a common flags like `-d`/`--debug`, `-v`/`--verbose`, `DEBUG` environment variable or similar debugging options. These flags should be implemented as global options, meaning they could be used in any command and in any combination.

Using one of these should print extra information about CLI’s execution steps. These logs should be printed to `stderr` and not to `stdout`. Reason for using `stderr` for debug output is to

### Saving local debug output to a file

There is a pattern of saving a debug output to a file. This file could be generated on every CLI execution that end with an error (see errors and exit codes). This creates a good experience for anyone experimenting a failure after a long-running command or an intermitten failure that’s hard to replicate. There are a few gotchas:

- Handling secrets - log files could contain secrets, [https://nodejs.org/api/report.html ](https://nodejs.org/api/report.html) saving them to disk without user’s knowledge might be dangerous. At the same time, finding a way to remove all secrets from a log file is a gargantuan task

Include a timestamp, include what was the original command. Include enough information to be able to reconstruct what led to the failure.

```bash
$ multipush do
Error!
Log saved to /tmp/sdadsa/tadsads/asdasd-timestamp.log
```

Don’t save this log file to a current directory `CWD`[^1] - there are a few reasons this is a dangerous and frustrating practice.

1. **Unexpectedly** creating a new files could cause issues for users. For example it _breaks_ `git` commands by introducing an unstaged file. It could break automations or scripts like linters expecting a specific number of files or their structure in a folder.
2. This file might contain sensitive information and could be accidentally committed, created in a shared directory on a server or included in a backup.

If you chose to
See dealing with filesystem.

## Self-hosted and self-rolled error tracking services

## 3rd party error tracking tools

## Private information

[^1]: Unless instructed with an Option
