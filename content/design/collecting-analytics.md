---
title: Collecting analytics
---

Using analytics tools to gain visibility into CLI and feature usage is something

If your CLI is used primarily as an API client, or is relying on API usage heavily, relying on a well formed HTTP User-agent header and API gateway analytics might give you good enough visibility. Consider this approach:

1. Gauge feature usage from the calls and request bodies you are receiving.
2. CLI versions and used platforms extracted from the User-agent string.
3. User usage patterns from API tokens or authorization information.

Trying to collect analytics from

## What to monitor

You will want to know about your users: what OS and version they are using, what

Another important metric is a success rate, were users able to run a command? Did it successfully exit with 0 or something else happened? Instrumenting this means you need to be extra careful to instrument your analytics code properly.
There are a few things to keep in mind:

- use language features to make sure that your analytics call can be executed with sufficient information even in case of a catastrophic failure.
- when your analytics call is the last thing your CLI is doing before exiting, there is a danger that it will hang if your Analytics API is too slow. Make your Analytics API as fast as possible[^1] and implement a sensible request timeout.
- If you decide to collect error messages and error logs, there will be Private information included in the stack trace. See how this is connected to debugging and error logging.

Collecting flags allows you to gauge usage of specific features. This is important when you decide to make a Breaking Change or deprecate something.

If your app interacts or depends on other tools, collecting their versions or configurations might very valuable.

## Privacy and security considerations

Collecting data from users machines should not be taken lightly. You could consider alternatives

## 3rd party analytics tools

Using such tools is tempting, however, because of the CLI lifecycle, you will see ancient versions. So rotating keys or secrets is not practical. Maybe you don’t care.

Sending HTTP requests to domains you don’t control is also making life complicated once you are dealing with firewalls.

Owning an API endpoint and then translating incoming requests analytics events you forward.

One more thing to keep in mind is that many analytics products are geared towards tracking user on a website. Many of the idiomatic things about tracking users on a web don’t translate well to the CLI usage.

## Self-hosting analytics tools

Similar to 3rd party analytics tools,

## Rolling your own

Connecting a CLI MySQL database storing CLI events or another event storage to Grafana or similar and making your own analytics is a viable, yet time-consuming option.

[^1]: Make the analytics processing asynchronous
