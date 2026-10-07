# Responsibilities and data flow

The main project is [homeassistant-stellantis-vehicles](https://github.com/andreadegiovine/homeassistant-stellantis-vehicles). It implements vehicle entities, polling, commands and OAuth token renewal. This repository exists to run that integration's browser-login helper locally instead of using its shared hosted helper. It does not replace the integration.

For one login attempt:

1. The integration constructs the brand-specific authorization URL and posts it, with email and password, to the configured worker.
2. The worker validates the request and rejects another simultaneous attempt with HTTP 429. It does not queue credentials or retry them against a different helper.
3. A fresh Chromium process opens the provider's login page. The worker observes login failures and the resulting app-scheme authorization-code redirect.
4. The worker returns the code and closes the browser. The integration exchanges the code with Stellantis, stores its tokens and completes setup.
5. Subsequent token renewal is the integration's responsibility. The worker is not a token store or a vehicle gateway.

The HTTP handler does not save credentials to disk. This is not a claim that every operating-system or browser component leaves no trace: review diagnostics before sharing, and treat the host as trusted. Debug screenshot/source export in the browser module is for deliberate developer use and is not enabled by the HTTP handler.

## Network boundaries

The default HA OS configuration exposes port 3000 only inside the Supervisor network. Home Assistant resolves the add-on hostname there. The hostname depends on the repository URL, so a different fork gets a different address.

There is no authentication layer on the worker API. Keep it on a trusted internal network. We do not host a public credential-processing service. The project website and source hosting are for documentation/downloads only.

The API accepts an authorization URL from the client and launches a browser against it. Network isolation is therefore important for preventing arbitrary callers from making browser requests through this host. The current worker does not implement a provider-domain allowlist or origin validation.

Supervisor discovery is best effort. The installed integration must implement the corresponding discovery flow; manual configuration is the supported fallback. Discovery does not guarantee authentication and does not authorize silently replacing a user's chosen custom helper.

## Compatibility

The intended wire contract is the existing integration's POST login request and `{ "code": "..." }` response. Brand compatibility follows the integration and provider behavior; this standalone release has not independently validated all brands. It is not a generic OAuth proxy.

Home Assistant OS can install the repository as an app/add-on. HA Container does not have the same Supervisor installation path. A separate plain-Docker distribution is not release-tested yet; do not assume Supervisor discovery or the add-on entrypoint work unchanged outside HA OS.
