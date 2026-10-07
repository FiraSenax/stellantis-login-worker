# Validation and release status

## What has been verified

- The earlier local HA 2026.9.4 deployment authenticated MyPeugeot through a local helper in approximately ten seconds; the integration then reported `loaded` with no entry error.
- That deployment combined a stable-version integration backport, separate refresh-lock work and an earlier worker build. It is not an end-to-end validation of this standalone repository's exact dependency set.
- Automated unit tests use synthetic data and fake responses. They cover deadlines, cancellation, concurrent requests, malformed inputs, missing codes, provider errors and log redaction.
- Dependency checks install the hash-pinned requirements, run pip check, audit known Python advisories and exercise the HTTP smoke suite.
- Image CI targets amd64 and aarch64. Consult the repository's Actions tab for the current commit's actual result; a workflow being present does not mean it has passed.

## Required before a stable release

1. Unit, dependency and image jobs pass for the exact release commit.
2. Install the add-on on a test HA OS host. Confirm its startup URL and internal health endpoint.
3. Complete a real login manually. Confirm both code acquisition and integration setup, without sharing credentials or authorization URLs in reports.
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
