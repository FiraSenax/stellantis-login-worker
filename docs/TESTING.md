# Validation and release status

## Live candidate test on 2026-10-09

The standalone worker **0.3.0-rc.2**, runtime/build commit `44a0f73`, was built
from source by Supervisor and installed alongside the previous worker on an
**amd64 / generic-x86-64** Home Assistant OS 18.3 host (Supervisor 2026.09.3,
Home Assistant Core 2026.9.4). The integration was the existing locally patched
2026.9.4 deployment, not the exact upstream develop-branch PR.

- The add-on started and its internal `/health` endpoint returned `{"status":"ok"}`.
- A user-driven MyPeugeot reauthentication reached this candidate. The worker
  reported authorization-code capture about twelve seconds after login started
  and returned HTTP 200. No code, credentials or account identifiers are recorded here.
- The integration persisted the new local helper URL, and a freshly loaded
  integration page showed the configured account without a setup/reauth error.
- An initial attempt still targeted the stopped previous worker and failed at
  DNS resolution. The candidate received no login for that attempt. Re-entering
  the new URL through normal keyboard input preceded the successful attempt;
  the evidence does not establish a backend URL-selection defect.

This validates one MyPeugeot login using the **locally source-built amd64
standalone candidate**. It does not validate a real aarch64 login, the exact
upstream add-on images, every provider, restart recovery or natural token renewal.
Native CI image builds and bundled Chromium startup for this runtime/build commit
passed on both architectures: [image run](https://github.com/FiraSenax/stellantis-login-worker/actions/runs/37912905710).

## Earlier validation

- The earlier local HA 2026.9.4 deployment authenticated MyPeugeot through a local helper in approximately ten seconds; the integration then reported `loaded` with no entry error.
- That deployment combined a stable-version integration backport, separate refresh-lock work and an earlier worker build. It is not an end-to-end validation of this standalone repository's exact dependency set.
- Automated unit tests use synthetic data and fake responses. They cover deadlines, cancellation, concurrent requests, malformed inputs, missing codes, provider errors and log redaction.
- Dependency checks install the hash-pinned requirements, run pip check, audit known Python advisories and exercise the HTTP smoke suite.
- Native amd64 and aarch64 image builds and bundled Chromium startup passed for runtime/build commit `a90ee31`: [image run](https://github.com/FiraSenax/stellantis-login-worker/actions/runs/37585217970). The browser smoke test uses an in-memory page and does not access the provider.
- Python 3.11 and 3.14 unit jobs and the dependency audit are also green for that commit. Consult Actions for subsequent changes; these results are not a guarantee about a different commit.

## Required before a stable release

1. Unit, dependency and image jobs pass for the exact release commit.
2. Install the add-on on a test HA OS host. Confirm its startup URL and internal health endpoint.
3. Complete a real login manually with the exact candidate image on both amd64 and aarch64. Confirm both code acquisition and integration setup, without sharing credentials or authorization URLs in reports.
4. Verify an unavailable helper and a rejected login produce a bounded failure. Confirm no credentials are automatically retried against a public service.
5. Check restart and reauthentication behavior in the integration. Observe natural token refresh; do not force repeated provider logins just to increase a test count.
6. If publishing prebuilt images, verify anonymous pulls for both architectures before adding an `image:` entry to config.yaml.

Until then, the source-built package remains a release candidate. An add-on update does not repair an already rejected refresh grant by itself.

## Running tests locally

Use Python 3.11 or 3.14 in an isolated environment. Install the runtime requirements with hashes, then pytest and pytest-asyncio. Run:

```sh
python -m pytest -q stellantis_login_worker/tests
python stellantis_login_worker/tests/smoke_worker.py
```

The smoke test binds a loopback HTTP server but does not log in to Stellantis. A clean vulnerability scan means no findings in the queried advisory data, not a complete security review.
