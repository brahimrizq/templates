# EmDash 1.4.0 Dokploy template evidence

Implementation: [Dokploy/templates #1308](https://github.com/Dokploy/templates/pull/1308), head `504c49ad0635bf1887c3955aff0f6d840f8d1e5a`.

The application was built from the unmodified upstream Dockerfile at `10beb8c5a99e41c3a8554b09ac5123c4dddc8d11` (`emdash@1.4.0`). Tests ran on 2026-11-08 with disposable Dokploy v0.30.8, Traefik v3.6.7, and Linux amd64. The fixture's Swarm ran inside Docker-in-Docker; the host's Swarm was not modified.

The screenshots contain synthetic editorial content and a synthetic administrator. Passkey private keys, cookies, credentials, fixture state, and raw deployment logs are excluded.

## Verified behavior

- Metadata, blueprint structure, Compose/TOML validators, official logo comparison, and production catalog build.
- Fresh Dokploy import and a real Git-context build/deployment without optional provider credentials or an encryption key.
- Administrator setup with a CDP virtual WebAuthn authenticator, HTTPS through Traefik, logout and passkey login. Setup nonce: Secure, HttpOnly, SameSite=Strict. Session: Secure, HttpOnly, SameSite=Lax.
- Post created and saved through the editor; additional publishing/media/scheduling operations exercised the authenticated application API from the browser's origin.
- Published page and body text, private draft rejection, uploaded PNG byte equality, and real scheduled publication by the continuously running Node process.
- Application container removal followed by a new Dokploy deployment. A fresh browser signed in with the same persisted passkey; published/draft content, scheduler output, media bytes, and settings survived.
- Production developer auth/reset endpoints returned 403. Anonymous private content API returned 401.
- Server UTC with a Europe/Paris browser; no page or console errors in the tested editorial flow.
- Runtime production startup, two persistent volume targets, no host ports, one isolated application network, SQLite integrity, one administrator, and the packaged official encryption-key generator/fingerprint command.
- The bot's catalog preview exported byte-identical raw Compose/TOML. That exact Base64 was imported into a second fresh service; setup, publishing, media, background publication, and container-recreation persistence passed independently. Its volumes and application network were distinct.
- Pausing Node caused Docker's health state to become unhealthy; resuming it restored healthy status.
- Anonymous home, published-post, and scheduler-created pages returned 200 in both installations. There were no JavaScript page errors. With no favicon selected, Chrome's implicit `/favicon.ico` probe returned 404; this is recorded in `public-browser-results.json`, separately from the error-free admin flow.

Initial, persistence, preview, runtime, and health results are recorded separately. The virtual authenticator exercised the production passkey endpoints, including fresh login after container recreation.

## Boundaries

HTTPS used Traefik's local test certificate with browser certificate checking relaxed. Public ACME issuance, real hardware/passkey sync services, non-amd64 behavior, a full disaster-recovery restore, live email/OAuth/AI providers, and plugin sandbox execution were not tested. No external messages were sent. The stock upstream Blog build has no email provider or sandbox runner. Filesystem sessions can be lost on container recreation, so the persistence check deliberately signs in again. Runtime-only origin configuration leaves local media unoptimized, as documented upstream.

The empty-site favicon request can be avoided by selecting a favicon in Settings → General. The tests preserve and report the upstream default rather than modifying the application image.

All public evidence is on a separate branch; the implementation PR contains only the blueprint's seven text/vector files.
