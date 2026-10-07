# Local Stellantis Login

This project exists to let users of the existing [Stellantis Vehicles HACS integration](https://github.com/andreadegiovine/homeassistant-stellantis-vehicles). use that integration without its shared, externally hosted login helper. The vehicle integration is the main project: it supplies the entities, vehicle data and commands. This add-on only runs the browser-login step on your own Home Assistant machine.

**Release candidate, not a stable release.** This standalone packaging combines tested login-recovery changes and newly locked dependencies. That exact combination still needs complete image builds and a live login test. The earlier local deployment authenticated successfully, but that does not validate every new dependency or long-term token renewal.

## Home Assistant OS installation

[Add this app repository to Home Assistant](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2FFiraSenax%2Fstellantis-login-worker)

1. Add the repository using the link, or enter `https://github.com/FiraSenax/stellantis-login-worker` under the app/add-on store's repository settings.
2. Install **Stellantis Login Worker**, then start it. This preview builds from source on your host; public prebuilt images are not yet provided, and installation may take several minutes.
3. Read the internal helper URL from the add-on log. In the HACS integration's login form, enter that address in **Login service URL** before entering credentials. Do not use an internal hostname copied from another installation.
4. Sign in and confirm that the integration loads. Integration versions supporting persistent login-service preferences can save the address in **Reconfigure > Global preferences** before reauthentication. Older versions save it when setup succeeds. Automatic Supervisor discovery depends on integration support; entering the URL manually remains supported.

The default configuration does not publish a host port. Your email and password are submitted to your local worker, which contacts Stellantis. We do not operate a shared login server. Vehicle data and token renewal still require Stellantis services.

[Website auf Deutsch](https://firasenax.github.io/stellantis-login-worker/de.html) · [Website in English](https://firasenax.github.io/stellantis-login-worker/)

See [add-on documentation](stellantis_login_worker/DOCS.md) for troubleshooting and [dependency maintenance](docs/DEPENDENCIES.md) for updates.

## Development

```sh
python3.11 -m venv .venv
. .venv/bin/activate
pip install --require-hashes -r stellantis_login_worker/requirements.txt
pip install pytest pytest-asyncio
pytest -q stellantis_login_worker/tests
python stellantis_login_worker/tests/smoke_worker.py
```

Tests use synthetic credentials and fake browser/provider responses. CI also builds test images for amd64 and aarch64. Builds do not publish images automatically.

## License and origin

Based on Kilian Taubmann's MIT-licensed [StellantisHAAddon](https://github.com/ktaubmann/StellantisHAAddon). Original copyright and license notices are retained in [LICENSE](LICENSE) and [NOTICE](NOTICE), including inside built images. This repository distributes only the login worker; it does not vendor the vehicle integration or copy code from the unlicensed worker-v2 project. Upstream improvements remain submitted separately.

Python dependencies, Chromium and the base operating system retain their own licenses and notices; the MIT license here does not relicense those components. This is an independent community project, not an official Stellantis product. Project licensing does not grant Stellantis trademark rights or permission under the provider's service terms.
