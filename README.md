# Deploy and Host JMAP Webmail on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/stalwart-webmail?referralCode=sLjDXb&utm_medium=integration&utm_source=button&utm_campaign=stalwart-webmail)

**A modern webmail for any JMAP server.** [Stalwart](https://stalw.art) is a
superb mail server that ships without a webmail UI; Fastmail, Cyrus and Apache
James speak JMAP too. This template is a fast, offline-capable JMAP mail client
you point at your server — one container, two variables, done.

## About Hosting JMAP Webmail

A single SvelteKit (Node) service with a volume at `/app/.data` for its SQLite
store: sessions, rate limits, settings and push subscriptions. Set `PUBLIC_JMAP_SERVER_URL` to your
Stalwart server's base URL (e.g. `https://mail.example.com`) — everything
else has working defaults, and `SESSION_SECRET` is auto-generated. Your users
sign in with their mailbox email + password (app passwords and
`password$totp` TOTP suffixes work); credentials are AES-256-GCM-sealed in a
server-side SQLite store, the browser cookie only ever holds an opaque
session id, and all JMAP traffic is proxied same-origin. The client is Zaur Mail 2: mail,
contacts, calendar and server-side filter rules in one app, with drafts and
an outbox that keep working offline, live updates over JMAP push, and phone
and tablet layouts. It installs as a PWA. There's a [Stalwart template](https://railway.com/deploy/stalwart?referralCode=sLjDXb)
on Railway too — deploy both for a complete self-hosted mail stack.

## Common Use Cases

- Give a self-hosted Stalwart server the webmail UI it doesn't ship with
- Small teams and families running their own mail who want a fast, modern
  client with contacts, calendars and vacation responses
- A browser and phone client for any JMAP account, with new-mail push
  notifications once you add VAPID keys

## Dependencies for JMAP Webmail Hosting

- A JMAP mail server — Stalwart, Fastmail, Cyrus or Apache James. This is a
  client, it does not run a mail server
- Everything else is bundled: one service, one volume, no database to manage

### Deployment Dependencies

- [Zaur Mail 2 README](https://github.com/nomideusz/zaur/blob/main/apps/mail2/README.md)
- [Source on GitHub](https://github.com/nomideusz/zaur) (`apps/mail2`)
- [Stalwart mail server](https://stalw.art) — pair with the
  [Stalwart template](https://railway.com/deploy/stalwart?referralCode=sLjDXb)

### Implementation Details

**Recommended: Stalwart OAuth.** With OAuth the server stores revocable
tokens instead of the password:

1. In Stalwart, register an OAuth client — redirect URI
   `https://<your-webmail-domain>/api/auth/oauth/callback`, PKCE S256.
2. Set `STALWART_OAUTH_ENABLED=true`, `STALWART_OAUTH_ISSUER_URL` (usually
   your JMAP URL) and `STALWART_OAUTH_CLIENT_ID`.
3. Password sign-in disables itself automatically.

Notes:

- **Push notifications:** generate keys with `npx web-push generate-vapid-keys`
  and set `VAPID_PUBLIC_KEY`, `VAPID_PRIVATE_KEY` and `VAPID_SUBJECT`. Without
  them the app still updates live while open; it just can't notify when closed.
- The interface is neutral "Webmail" by default; rebrand it with
  `PUBLIC_APP_NAME`, `PUBLIC_BRAND_NAME`, `PUBLIC_MAIL_DOMAIN` and `PUBLIC_APP_ICON`.
- Calendars, quotas and vacation responses are feature-probed, so the client
  degrades gracefully with your server configuration.
- **Other JMAP servers:** set `JMAP_AUTH=basic` for Cyrus or Apache James, or
  `JMAP_AUTH=token` for Fastmail (users paste an API token as the password,
  `PUBLIC_JMAP_SERVER_URL=https://api.fastmail.com/jmap/session`). Calendar,
  contacts, files, filters and vacation replies only appear when the server
  offers them.
- Signup and password-reset flows stay hidden unless you wire the optional
  register service — manage accounts in Stalwart directly.
- `PORT=3000`, `HOST=::` (dual-stack for Railway networking) and
  `RAILWAY_RUN_UID=0` (volume write access) are pre-configured — leave them
  as they are.

## Why Deploy JMAP Webmail on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway
will host your infrastructure so you don't have to deal with configuration,
while allowing you to vertically and horizontally scale it.

By deploying JMAP Webmail on Railway, you are one step closer to
supporting a complete full-stack application with minimal burden. Host your
servers, databases, AI agents, and more on Railway.
