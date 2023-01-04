---
title: Networking
---

What to consider when your CLI need to make network requests? Solid networking is a sign of a mature CLI app.

If your CLI needs to make a network request to call an API, fetch a resource or send analytics data, you should be aware of a few pitfals you can encounter. Your language/framework networking libraries will try to take care of some of these pitfals.

For this reason, it might be a good idea to centralize networking functionality in your CLI into a single shared module. This allows you to stick to a single implementation. Otherwise you are at risk of inconsistencies like some of your requests working while others are failing under certain configurations.

## Security

Note:
This section is from the CLI’s point-of-view. Securing network traffic and data in transport is too big of a topic. See OWASP or similar for more broader guidelines: [https://owasp.org/](https://owasp.org/)

Use HTTPS traffic when possible. Otherwise you will run into firewall rules blocking everything else.

Token and secret storage: see standalone section on secret storage

### When proxies are involved

See below for more information about supporting different proxy setups.

Certain man-in-the-middle (MITM) proxies are known to break certificate chain. Such proxies often replace upstream API server’s certificate with their own. This also breaks Certificate pinning and similar techniques. There are multiple non-exclusive strategies to implement:

- Load and use trusted certificate store from the OS running your CLI. In some enviroment, the MITM proxy-provided certificate is trusted on the system level. Respecting the system’s trust store allows you to make requests
- Allow users to provide their own trusted Certificate Authority (CA) file through CLI options.
- Create an option like`--insecure` to bypass certificate validation. This should be treated like a _hack_ to get things working. Consider marking is dangerous in documentation and accompanying this with a warning message, urging users to resolve the need for this.

## IPv4 & IPv6 considerations

## Proxies

Proxies are more common than you think and it’s a good idea to make your CLI app proxy-aware. There are many different flavours of proxy setups you can encounter. There is a good chance that your current tooling/framework already supports most of these.

Some proxies will act only as a tunnel, to allow your tool to communicate with users’ internal tools and service. Some will act as man-in-the-middle, trying to read your CLI app’s traffic for logging or auditing. Some will act as gatekeepers, blocking or modifying your traffic - for example to prevent leaks of private information.

Warning:
Replacing or upgrading networking libraries comes with a high risk of breaking proxy-related functionality. This is amplified by the fact that testing many of such configurations is very time consuming and brittle.

// IDEA: a test harness for this?

### HTTP and HTTPS proxies

The most common and basic type you will encounter is the HTTP/HTTPS proxy. This is most commonly configured with environment variables `HTTP_PROXY`/`http_proxy` or `HTTPS_PROXY`/`https_proxy`.

Be careful!

- There isn’t a formal standard for values! It’s implementation dependent.
- `HTTPS_PROXY` proxy itself does not need to be an HTTPS service. It only defines that _HTTPS traffic_ will be routed through it!
- Mermaid explaining HTTP and HTTPS proxies are meaning the traffic that flows through it.
- If you are using other transport mechanisms and protocols, you might have to implement proxy configuration options for these as well. E.g. `FTP_PROXY`

Note on priority:
In rare cases, you will encounter both lowercase and uppercase `http_proxy` & `HTTP_PROXY` environment variables setup! Here you should make conscious decision to prefer one over the other. Albeit capitalized version is more common today, because environment variables are nowadays usually defined in capitals. But the lowercase `http_proxy` predates its capitalized version, it’s usually taking a precedens. It might be a good idea to log which proxy is used for each request as part of you debug output.

There isn’t a formal standard for these!

You may also encounter a `NO_PROXY` variable in the wild. Support and feature set here is even more complicated than in the case of `HTTP_PROXY` options. Again, listen to your users and be mindful of which implementation you want to use and document it. [https://about.gitlab.com/blog/2021/01/27/we-need-to-talk-no-proxy/](https://about.gitlab.com/blog/2021/01/27/we-need-to-talk-no-proxy/)

## Using transport mechanisms other than HTTP

There are other transport mechanisms and protocols your CLI might rely on.

It’s a good idea to have a fallback to HTTP. Good example is a Socket.io’s WebSocket implementation that fallbacks to long-polling HTTP when WebSockets are not available/blocked.

## Allowed domains and IP ranges _protip_

It’s a good idea to create a list of domains and/or IP ranges that the CLI will be connecting to. This could be your API/Auth servers, CDN for static files, analytics provider etc.

This list will help users & integrators with audits or network teams creating firewall exception for your CLI.

It could also be integrated into your networking module to create an allowed list of domains.

## Setting an identifier header

Including a specific header into your CLI requests, like an User Agent or your own `X- header` will allow others to recognize your CLI version and act on it, in case you are calling APIs you don’t control. For example cURL’s default user agent is …

You may want to include multiple pieces of information to your custom user-agent:

- App version
- Platform (OS, architecture…)
