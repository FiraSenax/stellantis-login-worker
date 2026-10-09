## 0.3.0-rc.2

- Login attempts have one HTTP deadline and bounded browser cleanup.
- Concurrent requests now receive HTTP 429 with `Retry-After: 10` instead of
  waiting in a credentials queue. Reauthenticate multiple accounts one at a time;
  retry the other account after the active login has completed.
- Return known, locally constructed failure reasons; unexpected browser errors
  keep a generic response without page text, credentials or token values.
- Keep the redacted stuck-page URL even without a debug directory. Debug logs
  include ForgeRock HTTP/numeric status, known callback types and console event
  levels; raw response bodies and console contents are omitted.
- Only documented login rejections abort early. Pending verification and unknown
  numeric provider codes keep waiting for the browser redirect within the deadline.
- CI reads base images and OCI labels from build.yaml. Source-build installation,
  repository identity and pinned runtime dependencies remain unchanged.
- This candidate still requires exact-image login validation before release.

# Changelog

## 0.3.0-rc.1

- Standalone maintained distribution of the MIT-licensed local login worker.
- Shared login deadline, immediate busy responses and safe HTTP error messages.
- Provider-error detection and cleanup of pending login work.
- Locked runtime dependencies, automated checks and test image builds.
- Source-built preview; public prebuilt images and live release validation pending.
- Native amd64/aarch64 image builds and bundled Chromium startup verified.
