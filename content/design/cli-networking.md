---
title: Networking
---

What to consider when your CLI application needs to make network requests? Solid yet flexible networking is a sign of a mature CLI app. [Different flavors of proxies]({{< relref "#https-proxies" >}}), custom certificates, and additional auditing & security layers are very common.

<!--more-->

If your CLI needs to make a network request to call an API, fetch a resource or [send analytics data]({{< relref "collecting-analytics" >}}), you should be aware of a few pitfalls you can encounter. Your language/framework networking libraries will try to take care of some of these. But you should be aware of what they support and where they fall short.

## Architecture

Replacing or upgrading networking libraries, frameworks or languages comes with a **high risk of altering some functionality**. This is amplified by the fact that testing many such configurations, that your users rely on, is very time-consuming and brittle.

<!-- // IDEA: a generic test harness for this? -->

For this reason, it might be a good idea to **centralize networking functionality in your CLI into a single shared module**. This allows you to stick to a single implementation. Otherwise, you are at risk of inconsistencies like some of your requests working while others are failing under certain configurations.

```mermaid
graph LR
  API[Your API Server]

  subgraph User [User's machine and infrastructure]
  direction LR
    subgraph CLI [CLI Application]
    direction TB
    networkModule["Central HTTP/Networking module"]
    m1[Business Logic Module A] --> networkModule
    m2[Business Logic Module B] --> networkModule
    m3[Business Logic Module C] --> networkModule
    end
  environmentSettings[Environment and options] -.-> networkModule
  customCertificates[Custom Certificates] -.-> networkModule
  networkModule -.-> proxy[HTTP Proxy]
  end
  proxy -.-> API
  networkModule --> API
```

## HTTP(S) Proxies

Proxies are more common than you think and it’s a good idea to **make your CLI app _proxy-aware_**. There are many different flavors of proxy setups you can encounter. There is a good chance that your current tooling/framework already supports the most common ones.

**Some proxies will act only as a tunnel**, to allow your tool to communicate with users’ internal tools and services. **Some will act as a man-in-the-middle**, trying to read your CLI app’s traffic for logging or auditing. Some will act as gatekeepers, blocking or modifying your traffic - for example, to prevent leaks of private information.

![Transparent HTTP proxies work like switchboard operators](/images/museums-victoria-TVe0IEdsVc8-unsplash.jpg)

Let's take an example where your CLI Application needs to make a GET request to your API service. Your API service is available on HTTPS and its URL is `https://api.my.service`. In your application, you are making a call similar to this:

```python
requests.get('https://api.my.service')
```

### No proxies setup

When making an API call without any proxy configured, your request will get routed directly to the API server using the defined protocol like `https://`.

```mermaid
graph LR
  CLI[CLI Application] -->|"HTTP(S) GET request"| API[Your API server]
```

In this scenario, your CLI talks directly to your API server. And if the request is using `https://` protocol it does a [TLS handshake](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake/) and receives the reply in a secure tunnel.

### HTTP or HTTPS proxy

The most common and basic type you will encounter is the HTTP(S) proxy. This is most commonly configured with environment variables `HTTP_PROXY`/`http_proxy` or `HTTPS_PROXY`/`https_proxy`. You could also encounter a [`NO_PROXY`/`no_proxy` value]({{< relref "#no_proxy" >}}).

For example, if you have only `HTTP_PROXY=http://proxy.internal:9000` defined in environment variables, then **only `HTTP` requests will use it**. `HTTPS` requests will try to connect directly to the API server.

```mermaid
graph LR
  CLI[CLI Application with HTTP_PROXY configured]
  Proxy[Proxy server]
  API[Your API server]
  CLI -->|"HTTP GET request"| Proxy
  Proxy -->|Forwards request| API
  CLI -->|"HTTPS GET request"| API
```

Be careful!

- **There isn’t a formal standard** for values! It’s implementation dependent.
- `HTTPS_PROXY` proxy itself **does not need to be an HTTPS service**. It only defines that _HTTPS traffic_ will be routed through it! E.g. `HTTPS_PROXY=http://proxy.internal:9000` is a valid scenario.
- If you are using [other transport mechanisms and protocols]({{< relref "#using-transport-mechanisms-other-than-http" >}}), you might have to implement proxy configuration options for these as well. E.g. `FTP_PROXY`

In some languages and frameworks, **you might need to explicitly pass values of `HTTP_PROXY` and `HTTPS_PROXY`** to your networking library. In other languages, it might be enough to set these environment variables and the networking library will pick them up automatically.

{{% callout %}}

**Note on priority**

In rare cases, you will encounter _both lowercase and uppercase_ `http_proxy` & `HTTP_PROXY` environment variables setup. You should make a conscious decision to prefer one over the other. The **lowercase `http_proxy` predates its capitalized version**, it’s usually taking a precedent. Albeit **capitalized version is more common today** because environment variables are nowadays usually defined in capitals. It might be a good idea to log which proxy is used for each request as part of your debug output.
{{% /callout %}}

#### NO_PROXY

You may also encounter a `NO_PROXY` variable in the wild. Support and feature set here are even more complicated than in the case of `HTTP_PROXY` options. Again, [listen to your users]({{< relref "#a-word-on-a-reasonable-support" >}}) and be mindful of which implementation you want to use and document it.

{{< cite "https://about.gitlab.com/blog/2021/01/27/we-need-to-talk-no-proxy/" >}}

### Consider TLS termination point

You can **imagine a TLS connection as a tunnel**. It has two ends (termination points), that on creation perform a cryptographic dance known as [TLS handshake](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake/). After this connection is established, anything sent between those two ends is secure and _can't_ be read or modified by anyone else.

On top of that, **these two ends can also verify each other's identity** - for example with a practice called certificate pinning. This is how you can be sure that you are talking to the right server and not a malicious actor. However, there are scenarios where proxies could intercept this tunnel and create a different termination point. Allowing whoever is controlling this point to read and modify your traffic.

This has legitimate use cases, especially in the context of auditing and logging. And sometimes it's a requirement and limitation of enterprise environments. Let's take a look at some examples:

If the proxy server is acting as a **transparent proxy**, your CLI application will be communicating with the proxy server over HTTP. But the TLS handshake and certificate validation with the upstream API server will be performed by the CLI.

```mermaid
sequenceDiagram
  autonumber
  participant CLI as CLI Application
  participant Proxy as Proxy server
  participant API as Your API server
  CLI->>Proxy: HTTP Connection to Proxy
  activate Proxy
  Proxy->>CLI: Proxy is ready to receive request
  CLI-->API: CLI opens HTTPS connection to API through Proxy
  activate API
  CLI->>API: CLI sends request to the API
  API->>CLI: Sends response through Proxy
  deactivate API
  deactivate Proxy
```

However, if the Proxy is acting as a Man-in-the-middle / _Interception_ proxy, it will be terminating the TLS connection on its own. And your CLI will be presented a surrogate certificate/identity that doesn't match the API server.

```mermaid
sequenceDiagram
  autonumber
  participant CLI as CLI Application
  participant Proxy as Man-in-the-middle Proxy server
  participant API as Your API server
  CLI->>Proxy: HTTP Connection to Proxy
  activate Proxy
  Proxy->>CLI: Proxy is ready to receive request
  activate Proxy
  critical Proxy creates a surrogate TLS connection for CLI
  CLI-->>Proxy: CLI opens HTTPS connection to the API, but it's Proxy responding
  Proxy-->>API: Proxy opens HTTPS connection to the API
  activate API
  CLI->>Proxy: CLI sends request to the API, but it's talking to the Proxy
  Note over CLI,Proxy: Proxy could inspect the request and modify it
  Proxy->>API: Proxy sends request to the API
  API->>Proxy: Sends response
  deactivate API
  Note over CLI,Proxy: Proxy could inspect the response and modify it
  Proxy->>CLI: Sends response
  end
  deactivate Proxy
  deactivate Proxy
```

You should consider that **any well-intentioned security mechanism you'll put in your CLI, could prevent users from deploying** it in their environments. Allowing users enough flexibility, by defining their own trusted certificate authorities, and allowing custom routing rules e.g., when they expose your API server behind a reverse proxy under a different hostname will greatly help with adoption.

### Proxy authentication mechanisms

Some proxies will require your application to authenticate before a request could be fulfilled. Here are a few common types:

#### Token or credentials-based authentication

This type of authentication will **require your application to pass a secret key or credentials** along the connection request[^response407]. A very common type is Basic Authentication because it can be passed in the URL configuring the proxy connection. E.g., providing "username" and "password" as part of the URL will make your connection to the proxy server authenticated: `HTTPS_PROXY=http://username:password@proxy.internal:9000`.

You could encounter variations of this, often relying on a [Proxy-Authenticate header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Proxy-Authenticate) or similar. These schemes are **often supported by network stacks in modern languages and require no work from the application developer**.

#### Certificate-based

There are some situations where custom Certificate authorities and chains are involved in Proxy authentication. A common example is **Mutual TLS** - mTLS. This is a practice where both sides of the connection (client and server) are required to present a valid certificate.

#### Complex schemes

There are more complex scenarios like **[NTLM](https://en.wikipedia.org/wiki/NT_LAN_Manager) and [Kerberos](https://web.mit.edu/kerberos/)-based proxy authentication**. These often work as layers of larger enterprise deployments, with demanding access control rules. They also often rely on OS-level features like [IWA (Integrated Windows Authentication)](https://learn.microsoft.com/en-us/aspnet/web-api/overview/security/integrated-windows-authentication). Supporting these schemes in your application is often not trivial.

You could leverage tools like [Cntlm](http://cntlm.sourceforge.net) for NTLM/Kerberos support. Cntlm acts as a transparent HTTP proxy server for your CLI, but it can be configured to authenticate with NTLM or Kerberos. Without a need to support these schemes in your application.

It's common to see a layered combination of these authentication mechanisms. For example, a proxy server could require a client to present a valid certificate, and then use a token-based authentication to verify the client's identity.

### Using transport mechanisms other than HTTPS

There are other transport mechanisms and protocols your CLI might rely on. Protocols like WebRTC, SSH, FTP, WebSockets, and others. There are valid and good reasons to use these protocols. However, **you will run into issues with firewalls, proxy policies and other security and access control mechanisms**.

If feasible, it’s a good idea to have a **fallback to HTTPS**. Many of these protocols already have options to **tunnel through an HTTP connection**. A good example is Socket.io’s WebSocket implementation that [fallbacks to long-polling HTTP](https://socket.io/docs/v3/how-it-works/) when WebSockets are not available.

## IPv4 & IPv6 considerations

Depending on your application, this might be a non-issue. However, more and more often you will encounter **environments that are IPv6-only**. Some languages and networking libraries expect IPv4 by default and need to be configured to use IPv6. This is potentially further complicated in cloud/virtualized environments where some parts of the network are IPv4-only and others are IPv6-only.

[Check that the API server you are calling can also reply from the IPv6 network.](https://ipv6-test.com/validate.php) **Not all cloud providers offer this by default yet!** As a next step, test your application in an IPv6-only environment and make sure it works as expected.

## Allowed domains and IP ranges

It’s a good idea to create a **list of domains and/or IP ranges** that the CLI will be connecting to. This could be your API/Auth servers, CDN for static files, [analytics provider]({{< relref "collecting-analytics" >}}) etc.

This list will help users & integrators with audits or network teams create firewall exceptions for your CLI. It could also be integrated into your networking module to create an allowed list of domains.

On a related note, you could encounter users that will set up **a mirror or a reverse proxy in front of your API server**. This is a common practice to add additional security and access control layers, similar to access proxies. In this case, you should allow the user to specify the reverse proxy’s hostname.

Provide a configuration option, like a `--api=` flag or `API_HOSTNAME=` environment variable to overwrite the default `https://api.my.service` to `http://api-mirror.acme.internal:9001` if the user desires. Again, [centralizing this configuration]({{< relref "#architecture" >}}) will make it easier to implement this option.

## Setting an identifier header

Including a specific header in your CLI requests, like a [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) or your custom header will allow others to recognize your CLI and act on it. This is especially useful in case you are calling APIs you don’t control. For example cURL’s default user agent is `curl/7.84.0`.

You may want to include multiple pieces of information for your custom user-agent:

- Application name
- Application version
- Platform (OS, architecture…)

For the `multipush` example:

```text
User-Agent: multipush/1.0.0 (Linux; x86_64)
```

## Security

{{% callout %}}

This section is from the CLI’s point of view. Securing network traffic and data in transport is too big of a topic. See OWASP or similar for broader guidelines: [https://owasp.org/](https://owasp.org/)

{{% /callout %}}

Some takeaways from sections above:

- Use HTTPS whenever possible
- Allow users to define custom Certificates and Certificate Authorities

### The "insecure" option

Some users will want to disable the security measures you've put in place, like skipping TLS verification on HTTPS connections. This is a common practice in development environments, where the API server is running on localhost or a local network with an invalid self-signed certificate. This is also an abused option, that can put users at risk. **Whether to allow users to disable security measures, for example by introducing an `--insecure` flag, is a decision you will have to make.**

Your CLI application will at the end of the day run on a user’s machine. In an environment, they know the best. An argument could be made that the **user should be in control and should be able to configure or disable security measures**.

If you provide this option, be _loud_ in the output, that there are probably better ways than using the `--insecure`. Make sure that **this is a break-glass option, not an answer to every unusual network setup**. Provide documentation, examples and messaging in the CLI's output to help users use your CLI securely.

<!-- TODO: Token and secret storage: see the standalone section on secret storage -->

## A word on a reasonable support

There is still much more to be said about networking and different environments your CLI application might encounter. You can't support and test all the different scenarios. But you don't need to right away. **Listen to your users, and see how they use your application.**

Having good architecture and test coverage will help you prevent regressions. And will make it easier to add support for new environments and scenarios. Using a standard language library and being aware of its limitation will get you far. To achieve solid yet flexible networking, **rely on boring, standard and battle-tested tooling where possible**.

[^response407]: This is implemented as a _challenge-response_, where the proxy server will initially respond with a [407 status code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/407) and a `Proxy-Authenticate` header. The client will then respond with a `Proxy-Authorization` header.
