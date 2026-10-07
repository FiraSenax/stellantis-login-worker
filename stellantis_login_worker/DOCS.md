# Local login helper

Use this add-on alongside the existing Stellantis Vehicles HACS integration. The add-on runs Chromium locally to perform the provider's normal browser login. It is needed for initial login or reauthentication; normal vehicle polling and token refresh remain in the integration.

## Configuration

Start the add-on and copy the internal URL printed in its log into the integration's **Login service URL** field. The repository-specific hostname is resolved inside the Supervisor network. Do not enter `localhost`, which usually refers to Home Assistant's own container. If a compatible integration supports Supervisor discovery, it may offer setup automatically; otherwise configure the URL manually.

| Option | Default | Meaning |
|---|---|---|
| `log_level` | `info` | Log detail. Prefer info; avoid sharing unreviewed diagnostics. |
| `timeout` | `60` | Login deadline in seconds, excluding bounded cleanup. The effective maximum is 240 seconds; a client may specify its own deadline. |

No host port is published by default. The worker has no authentication of its own: do not expose it to the internet. If external container access is needed, restrict network access appropriately. Credentials are used for the login request and are not persisted by the HTTP handler. No fallback to a public helper is implemented here.

## Results

- `200`: an authorization code was obtained. The integration must still exchange it for tokens and finish setup.
- `400`: invalid input or an unsuccessful provider login.
- `429`: another login is running; wait before trying again. Requests are not queued.
- `504`: the overall login deadline expired.
- `500`/`502`: internal failure or missing authorization code.

The log records the last login phase and, where available, a numeric provider error. A timeout does not prove that a password is wrong or that CAPTCHA caused the problem. Interactive verification may require the integration's manual login method; this helper does not promise to solve provider challenges.

`GET /health` returns `{"status":"ok"}` when the HTTP service is reachable. It does not validate Chromium, credentials or Stellantis availability.

## Preview limitations

The release candidate builds locally from source. Public prebuilt images and the standalone package's end-to-end login are not yet release-validated. Existing installations of other forks are not migrated automatically; their hostnames and settings differ. Keep a working installation until you explicitly test and switch to this one.
