# Dependency maintenance

`stellantis_login_worker/requirements.in` contains compatibility ranges. The generated `requirements.txt` pins runtime dependencies and their distribution hashes. Docker uses `pip --require-hashes`.

Regenerate from the repository root with uv 0.12.23:

```sh
uv pip compile stellantis_login_worker/requirements.in --python-version 3.11 --python-platform linux --generate-hashes --no-header --upgrade -o stellantis_login_worker/requirements.txt
```

Review updates, run unit and smoke tests, build both supported architectures, and perform a real login before promoting a release. Pinning Playwright also determines the Chromium revision selected by its installer. Dependabot and weekly runtime vulnerability audits help identify updates; a clean audit is not a guarantee of security.

The OS base tags, apt packages, source-build tools and nested Actions are not fully pinned. These are reproducible Python dependency selections, not bit-for-bit reproducible images.
