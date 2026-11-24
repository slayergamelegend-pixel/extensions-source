# Aedex: login from the extension settings (kept for personal use)

This branch keeps the **settings-based login** that was originally proposed in keiyoushi/extensions-source#21377.

## How it works

- Settings -> Aedex -> "Iniciar sesion" asks for email and password in a dialog.
- A short WebView solves Cloudflare Turnstile only to get a token (no `X-Requested-With` problem, since no
  credentials are submitted through it).
- The credentials are POSTed with OkHttp to `/api/auth/callback/credentials` together with the Turnstile token.
- The session cookie ends up in the shared cookie jar; `refreshAccessToken()` reads `/api/auth/session` and stores
  the bearer token used by the reader.
- Only the access token is stored, never the password.

## Why it is not the upstream solution

Typing credentials into Settings is awkward for most users. The upstream PR moved to a login that happens in
Mihon's own WebView (the site's real login page), which needs the Keiyoushi Runtime to remove
`X-Requested-With`. See `docs/aedex/2026-11-24-login-webview-keiyoushi-runtime/` in the maintainer's notes.

## When to use this branch

- Fallback if the WebView login breaks or the runtime is not available.
- Personal builds, where the settings dialog is preferred.

Key files: `Login.kt` (`login`, `promptLogin`, `loginErrorMessage`), `Aedex.kt` (preferences, `refreshAccessToken`).
Last upstream-compatible commit: `f3655a5b2a`.
