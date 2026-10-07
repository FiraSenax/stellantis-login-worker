# Contributing

Keep changes focused on the local login helper. Vehicle entities, polling and remote commands belong in the [main integration](https://github.com/andreadegiovine/homeassistant-stellantis-vehicles). The helper originates in [StellantisHAAddon](https://github.com/ktaubmann/StellantisHAAddon); retain attribution and offer generally useful changes upstream.

Include a regression test for changed login or HTTP behavior. Tests must use synthetic credentials. Do not commit account details, tokens, provider callback URLs, debug page exports or screenshots of real login sessions.

For a bug report, include the add-on version, CPU architecture, HA version, last login phase and HTTP/numeric error code when available. Review and redact logs yourself before attaching them. Never post a password or authorization code. The maintainers do not need your account credentials.

Dependency updates must regenerate hashes and pass image tests on both supported architectures. See docs/DEPENDENCIES.md and docs/TESTING.md. Changes to major dependencies or provider interactions need their own review and real-login validation before release.
