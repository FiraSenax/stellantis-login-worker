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

## Concurrent logins and diagnostics

Only one browser login runs at a time. A second HTTP request receives **429**
with `Retry-After: 10`; it no longer queues credentials behind the running login.
When several accounts need reauthentication, finish one before retrying the next.
The header is a retry hint, not a promise that the browser will be free after ten
seconds. The worker does not automatically retry or switch to a hosted helper.

Known failures return a numeric login endpoint error or a fixed explanation such
as "Identity provider rejected the login". Unexpected exceptions remain generic.
Logs include the final phase and a URL with query values and fragments redacted,
even without a debug directory. With `log_level: debug`, ForgeRock authentication
responses report HTTP/numeric status and known callback types, and browser console
warnings/errors report their level. Raw console messages, response bodies, callback
values and page text are not logged. These structured diagnostics cannot preserve
every detail of a raw provider response.

A nonzero Gigya `errorCode` does not always mean final rejection. SAP documents
pending registration (206001), verification (206002) and further authentication
steps; screen-sets may continue handling these in the page. The worker terminates
early only for the documented rejection codes 401021, 401022, 403041, 403042,
403044 and 403120. Pending and unknown codes continue within the existing deadline;
this does not implement additional interactive verification or prove that the
provider will complete it automatically.

References: [SAP account error handling](https://help.sap.com/docs/SAP_CUSTOMER_DATA_CLOUD/8b8d6fffe113457094a17701f63e3d6a/9849bde7aff74b1f8074a6275a6bf7b3.html)
and [SAP response codes](https://help.sap.com/docs/SAP_CUSTOMER_DATA_CLOUD/8b8d6fffe113457094a17701f63e3d6a/416d41b170b21014bbc5a10ce4041860.html).
