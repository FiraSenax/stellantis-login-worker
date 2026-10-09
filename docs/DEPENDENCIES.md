# Dependency maintenance

`stellantis_login_worker/requirements.in` contains compatibility ranges. The generated `requirements.txt` pins runtime dependencies and their distribution hashes. Docker uses `pip --require-hashes`.

Regenerate from the repository root with uv 0.12.23:

```sh
uv pip compile stellantis_login_worker/requirements.in --python-version 3.11 --python-platform linux --generate-hashes --no-header --upgrade -o stellantis_login_worker/requirements.txt
```

Review updates, run unit and smoke tests, build both supported architectures, and perform a real login before promoting a release. Pinning Playwright also determines the Chromium revision selected by its installer. Dependabot and weekly runtime vulnerability audits help identify updates; a clean audit is not a guarantee of security.

The OS base tags, apt packages, source-build tools and nested Actions are not fully pinned. These are reproducible Python dependency selections, not bit-for-bit reproducible images.

## Reviewing Dependabot changes

The compiled requirements currently omit the generator header. Dependabot may
therefore edit `requirements.txt` directly without updating `requirements.in` or
resolving the full dependency graph. Treat these PRs as update proposals, not as
ready-to-merge lockfiles. Before merging, review the compatibility bounds in
`requirements.in`, adjust them when needed, rerun the documented `uv pip compile`
command for every affected add-on, and commit the regenerated hashed output.
Require hash-verified installation, `pip check`, audits and regression tests on
the resulting commit. Do not hand-edit hashes or merge an unregenerated lockfile.

CI reads each architecture's base image and all labels from the add-on's
`build.yaml`, so source builds and workflow builds use the same metadata.
The standalone workflow still only builds and tests; it does not publish images.
A dependency upgrade does not remove the manual login release gates in TESTING.md.
