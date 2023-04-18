---
title: Performance
---

Monitoring performance in CLI apps is a complex subject. On one hand, your CLI will be run on [a near infinite amount of system setups]({{< relref "cli-application-lifecycle" >}}) under different loads. And it will be performing operations with different complexity, depending on the user's inputs.

As a baseline, focus on a few metrics that will likely heavily influence your users. Installation time and startup time of your CLI. Keeping your CLI lightweight and snappy does wonders for _perceived performance_.

## Perceived performance

When talking about perceived performance on the terminal, we don’t necessarily mean how long it takes for CLI to finish its execution. Instead, it’s the speed, responsiveness and reliability _as perceived by the user_.

A faster feedback loop (on a mistyped flag for example, [see Configuration and arguments]({{< relref "configuration" >}})) makes it that much easier to recover from mistakes.

{{< cite "https://developer.mozilla.org/en-US/docs/Learn/Performance/Perceived_performance" >}}

## Collecting analytics

As discussed on the [CLI Analytics page]({{< relref "collecting-analytics" >}}), once you commit to collecting analytics directly from the CLI, you can also instrument important code paths to report execution time. Then, in your analytics you can discover trends and how performance changes between platforms, userbases and versions.

Keep in mind, that the data you’ll collect this way are dependent on your users' setup and machines.

## Synthetic tests

Synthetic performance testing a CLI application could also be fairly complicated. Some languages and frameworks are better equipped for profiling and performance testing. Depending on various code paths and settings that could be influencing this. You might get around these limitations by utilizing your CI test run time to monitor for performance regressions.
