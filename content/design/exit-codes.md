---
title: Exit codes
---

When your CLI app finishes, it needs to send an Exit code. A number, usually between 0 and 127. Exit code zero means „success“. Meaning of _success_ in this case is up to your interpretation and design goals. Anything above zero signals a failure of some sort.

<!--more-->

In most scenarios, you should exit with a non-zero exit code[^1] whenver CLI command couldn’t accomplish its task.

However, there is a grey area, where semantics and usage of the CLI matters a lot. A good example is an HTTP CLI client, like cURL or httpie. Should they fail with a non-zero exit code for all HTTP requests that connect, but responds with an HTTP code different than 200 OK? What about 301 Redirects?

## Keep it simple

There are many design considerations to take into account when thinking about exit codes.

- Is the failure recoverable or not? Could re-running the command fix it? Like an API server request timing out
- Is the outcome of the run a „failure“ or a „partial failure“ - if your CLI is doing multiple actions, but only some failed? Or only 1 out of a thousand operations failed?
- Should there be granular exit codes distinguishing between warning, errors and failures?
- Could we propagate Error codes as an Exit code?

You can’t be wrong if you stick to a binary _success-failure_ exit codes. Exit codes are rather limiting, not providing a mechanism to communicate an user-readable error message. Either exit with 0 or 1. If you don’t have a strong reason to use more at this point, then don’t. Instead provide users a good, parseable error or a warning message on `stderr`.

[^1]: However, it doesn’t _need_ to say anything. See [Messaging]({{< relref "messaging" >}})
