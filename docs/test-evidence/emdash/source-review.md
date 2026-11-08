# EmDash template source review

- Requested issue: Dokploy/templates #1305, Add Emdash CMS.
- Correct application: `emdash-cms/emdash`; the issue's `generalaction/emdash` resource is an unrelated desktop application.
- Stable release inspected: `emdash@1.4.0`, commit `10beb8c5a99e41c3a8554b09ac5123c4dddc8d11` (2026-10-29).
- Reviewed the complete issue/comments and Dossier PR #1240; searched all 1,045 listed open/closed template PRs across eleven API pages for EmDash, Em Dash, issue 1305, and generalaction. No implementation found at the initial review.
- The supplied root Compose builds the upstream Dockerfile, which packages the Blog theme. No official image reference or container-publishing workflow was found in the pinned source/documentation.
- The Dockerfile rewrites the Blog SQLite path to `/app/data/data.db`. Local media is `/app/uploads`. Both are persistent named volumes in the template.
- Node standalone production startup is unchanged. The scheduler runs in the Node process. Default filesystem sessions are not persisted by the two application volumes.
- Passkeys need the configured public HTTPS origin. First-site ownership is claimed through the setup wizard. Setup nonce uses Secure/HttpOnly/SameSite=Strict with the public origin.
- The health check uses setup/status, whose handler reads SQLite, rather than the lightweight version endpoint. End-to-end workflows still need separate verification.
- No email provider, OAuth provider, S3/PostgreSQL adapter, or plugin sandbox runner is installed in the stock Blog configuration. Their credentials are not deployment requirements. Build/configuration changes are needed to add them.
- Encryption key format is application-specific and canonical: `emdash_enc_v1_` + 43 base64url characters for 32 random bytes. The generic Dokploy helpers do not directly implement it. An optional blank value and official pinned CLI generation command are documented.
- Preview and IP-salt secrets are generated into SQLite by EmDash; there is no template-generated login/database password.
- Build-time origin is absent in the upstream Dockerfile. Runtime origin works for auth and links; local image optimization falls back to original files, as documented upstream.
- Upstream runtime runs as root. Local named volumes are writable without an ownership wrapper. Backups must include SQLite WAL/SHM when present, media, and separately saved encryption keys. JSON archives are not a complete restorable database backup.

Primary references: release-pinned root Dockerfile, compose.yaml, templates/blog/astro.config.mjs, packages/core/src/config/secrets.ts, packages/core/src/auth/trusted-proxy.ts, setup/admin.ts and setup/admin-verify.ts, and deployment/nodejs, deployment/secrets, guides/authentication, guides/email, and guides/backups documentation.
