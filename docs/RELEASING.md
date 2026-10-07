# Release process

A preview repository is not a stable-release announcement. Keep the current source-build path until both architecture builds and anonymous registry downloads have been verified.

1. Update config.yaml and CHANGELOG.md together. Do not reuse a published version for changed runtime files.
2. Run the unit, dependency and native image jobs against the exact commit. Both images must launch their bundled Chromium in the provider-free smoke test.
3. Complete the manual acceptance checks in TESTING.md on a test installation. Keep the working installation's URL available for rollback.
4. Publish a prerelease with explicit test scope before a stable release. Never include account data, live callback URLs or screenshots containing credentials in release notes.
5. Prebuilt distribution is a later packaging step: publish versioned images for both architectures, retain LICENSE and NOTICE in each, link packages to this repository, and explicitly verify anonymous pulls. New GHCR packages can start private even for a public repository. Do not add an image setting to config.yaml before public access works.
6. Update the website and README to reflect the actual installation method. Keep source attribution and main-integration links visible.

The current build workflow creates and tests images but does not push them to a registry. Documentation describes the source-build preview accordingly. Maintaining a central login service is outside this project's scope.
