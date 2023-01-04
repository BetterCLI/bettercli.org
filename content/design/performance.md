---
title: Performance
---

Monitoring performance in CLI apps is a complex subject. On one hand, your CLI will be run on near infinite amount of system setups (link to lifecycle) under different loads. And it will be performing operations with different complexity, depending on user’s inputs.

As a baseline, focus on a few metrics that will likely heavily influence your users. Installation time and startup time of your CLI. Keeping your CLI lightweight and snappy does wonders for _perceived performance_.

## Perceived performance

When talking about perceived performance on the terminal, we don’t necessarily mean how long does it take for CLI to finish its execution. Instead it’s the speed, responsivness and reliability _as perceived by the user_.

Faster feedback loop (on mistyped flag for example, see Args) makes it that much easier to recover from mistakes or misconfigurations.

## Collecting analytics

As discussed in the Analytics page, once you commit to collecting analytics directly from the CLI, you can also instrument important code paths to report execution time. Then, in your analytics you can discover trends and how performance

Keep in mind, that data you’ll collect this way are very dependent on your users’s setup and machines.

## Synthetic tests

Synthetic performance testing a CLI applications could also be fairly complicated. Some languages and frameworks are better setup for profiling and performance testing. Depending on various code paths and settings that could be influencing this. You might get around these limitations by utilizing your CI tests run time to monitor for performance regressions.
