# Deployment Guide: OMATrust Reputation & Attestation System

Operational runbook for deploying and configuring the Vercel projects that make up the OMATrust reputation and attestation system.

**Policy reference:** [deployment-model.md](./deployment-model.md) — branching model, environment semantics, URL conventions, security principles.

---

## 1. Projects Covered

| # | Project                    | Domain                  | Role                                     |
|---|----------------------------|-------------------------|------------------------------------------|
| 1 | `rep-attestation-frontend` | `app.omatrust.org`      | Portal — attestation creation & verification |
| 2 | `omatrust-backend`         | `backend.omatrust.org`  | APIs — auth, accounts, relay, RPC proxy  |
| 3 | `omatrust-widgets`         | `widgets.omatrust.org`  | Embeddable review widget (iframe)        |
| 4 | `omatrust-api-gateway`     | `api.omatrust.org`      | Edge proxy — public `/v1/*` API surface  |

---

## 2. Service URL Derivation & Domain Structure

Multiple projects (`rep-attestation-frontend`, `omatrust-api-gateway`) use a `CHAIN_PREFIX_MAP` in code to derive full service URLs from base domains. The active chain determines the subdomain prefix:

| Active Chain       | Prefix   | Example                        |
|--------------------|----------|--------------------------------|
| `omachain-mainnet` | (none)  | `backend.omatrust.org`         |
| `omachain-testnet` | `test.` | `test.backend.omatrust.org`    |
| `omachain-devnet`  | `dev.`  | `dev.backend.omatrust.org`     |

Changing the subdomain convention requires a code change in:
- `rep-attestation-frontend/src/lib/service-urls.ts`
- `omatrust-api-gateway/api/_shared/upstreams.ts`

Explicit domain env vars (e.g., `NEXT_PUBLIC_OMATRUST_BACKEND_DOMAIN`) override the default base domain when set.

### Pre-Mainnet Redirects

All projects use the same redirect pattern. Redirects are configured in **Vercel's Domains UI** (not code or env vars).

| Project Domain          | Redirects To                       | Type |
|-------------------------|------------------------------------|------|
| `app.omatrust.org`      | `preview.app.omatrust.org`         | 302  |
| `backend.omatrust.org`  | `preview.backend.omatrust.org`     | 302  |
| `widgets.omatrust.org`  | `preview.widgets.omatrust.org`     | 302  |
| `api.omatrust.org`      | `preview.api.omatrust.org`         | 302  |

### At Mainnet Launch (all projects)

1. Remove the 302 production redirect in Vercel Domains UI
2. Attach `<domain>` directly to the Production environment
3. Set active chain = `omachain-mainnet` on Production
4. Deploy (merge `staging` → `main`)
5. Redirect `preview.<domain>` → `test.<domain>` via 301 (the preview subdomain is permanently retired)

---

## 3. Common Environment Variables

These variables follow the same per-environment pattern across multiple projects. Set them once per Vercel environment; per-project sections only list what's unique to that project.

### Active Chain

Every project has an active chain variable. The name differs between Next.js frontends and backend services:

| Project                    | Variable Name              |
|----------------------------|----------------------------|
| `rep-attestation-frontend` | `NEXT_PUBLIC_ACTIVE_CHAIN` |
| `omatrust-widgets`         | `NEXT_PUBLIC_ACTIVE_CHAIN` |
| `omatrust-backend`         | `OMATRUST_ACTIVE_CHAIN`   |
| `omatrust-api-gateway`     | `OMATRUST_ACTIVE_CHAIN`   |

| Vercel Environment | Value              |
|--------------------|--------------------|
| Production         | `omachain-mainnet` |
| public-test        | `omachain-testnet` |
| Development        | `omachain-devnet`  |
| Preview            | `omachain-testnet` |

### Thirdweb Client ID

Shared by all Next.js frontend projects (`rep-attestation-frontend`, `omatrust-widgets`).

| Variable                         | Value      | Notes                       |
|----------------------------------|------------|-----------------------------|
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | `<shared>` | Same value all environments |

### Delegated Attestation Signing

Shared by projects that submit on-chain attestations (`rep-attestation-frontend`, `omatrust-backend`).

The code checks `THIRDWEB_SECRET_KEY` + `THIRDWEB_SERVER_WALLET_ADDRESS` first. If both are non-empty, it uses the Thirdweb Server Wallet. If either is empty, it falls back to `EAS_DELEGATE_PRIVATE_KEY`.

| Variable                         | Production    | public-test   | Development     | Preview         |
|----------------------------------|---------------|---------------|-----------------|-----------------|
| `THIRDWEB_SECRET_KEY`            | `<secret>`    | `<secret>`    | —               | —               |
| `THIRDWEB_SERVER_WALLET_ADDRESS` | `<address>`   | `<address>`   | —               | —               |
| `EAS_DELEGATE_PRIVATE_KEY`       | —             | —             | `<devnet-key>`  | `<testnet-key>` |

### Debug / Operational

| Variable                    | Production | public-test | Development | Preview |
|-----------------------------|------------|-------------|-------------|---------|
| `NEXT_PUBLIC_DEBUG_ADAPTER` | `false`    | `false`     | `true`      | `true`  |
| `MAINTENANCE_IN_PROGRESS`   | `false`    | `false`     | `false`     | `false` |

`MAINTENANCE_IN_PROGRESS` is only set to `true` during key rotation windows (see Section 8).

---

## 4. Common Vercel Environment Pattern

All projects follow the same Vercel environment structure. The pattern below applies to every project unless its per-project section notes otherwise.

### Preview Phase (Current)

| Setting          | public-test                       | Preview                 | Production                           |
|------------------|-----------------------------------|-------------------------|--------------------------------------|
| Type             | Custom                            | Built-in                | Built-in                             |
| Branch Tracking  | ON → `staging`                    | ON → all branches       | ON → `main`                          |
| Deployment       | Auto-deploy on push to `staging`  | Auto on PR              | Auto (302 redirect, app doesn't run) |
| Domain           | `preview.<domain>`                | (Vercel auto-generated) | `<domain>` → 302 redirect           |

### Target State (Post-Mainnet)

| Setting          | Production              | public-test              | Development              | Preview                 |
|------------------|-------------------------|--------------------------|--------------------------|-------------------------|
| Type             | Built-in                | Custom                   | Built-in                 | Built-in                |
| Branch Tracking  | ON → `main`            | OFF                       | ON → `staging`           | ON → all branches       |
| Deployment       | Auto on merge to `main`| Manual promotion only     | Auto on merge to `staging`| Auto on PR             |
| Domain           | `<domain>`             | `test.<domain>`           | `dev.<domain>`           | (Vercel auto-generated) |

### Common Transition Steps (per project)

When transitioning from preview to target state:

1. **Create Development environment** — Project Settings → Environments → enable Development, branch tracking ON → `staging`, add domain `dev.<domain>`
2. **Configure Production environment** — remove the 302 redirect from `<domain>`, attach `<domain>` directly to Production, set Production env vars (active chain = mainnet + project-specific vars)
3. **Reconfigure public-test** — branch tracking OFF, change domain from `preview.<domain>` → `test.<domain>`
4. **Configure DNS** — add CNAME records for `test.<domain>` and `dev.<domain>` → `cname.vercel-dns.com`
5. **Redirect preview subdomain** — configure `preview.<domain>` as 301 redirect to `test.<domain>`
6. **Verify** — push to staging (→ dev), promote to public-test (→ test), merge to main (→ production), confirm preview redirect

---

## 5. Per-Project: rep-attestation-frontend

**Repository:** `rep-attestation-frontend`
**Full env reference:** [`.env.example`](https://github.com/oma3dao/rep-attestation-frontend/blob/main/.env.example)
**Launch Priority:** This frontend launches before app-registry-frontend.

### Domains

| Environment | Preview Phase             | Target State            |
|-------------|---------------------------|-------------------------|
| public-test | `preview.app.omatrust.org`| `test.app.omatrust.org` |
| Production  | 302 → preview             | `app.omatrust.org`      |
| Development | —                         | `dev.app.omatrust.org`  |

### Project-Specific Variables

These are in addition to the common variables in Section 3.

| Variable                      | Purpose                                              | Env-Specific?                       |
|-------------------------------|------------------------------------------------------|-------------------------------------|
| `NEXT_PUBLIC_WIDGET_BASE_URL` | Widget iframe origin (for SDK signing bridge)        | Yes — matches widget domain per env |
| `MAX_GAS_PER_TX`              | Gas limit for delegated attestation (default: 800000)| No                                  |

The backend URL is derived automatically from the active chain via `CHAIN_PREFIX_MAP` (see Section 2). Override with `NEXT_PUBLIC_OMATRUST_BACKEND_DOMAIN` only for local dev.

### Contract Dependencies

| Contract       | Purpose                               |
|----------------|---------------------------------------|
| EAS            | Attestation creation and verification |
| SchemaRegistry | Schema registration and lookup        |

Contract addresses are configured in [`src/config/chains.ts`](https://github.com/oma3dao/rep-attestation-frontend/blob/main/src/config/chains.ts).

| Chain             | Chain ID | Attestation Service |
|-------------------|----------|---------------------|
| OMAChain Testnet  | 66238    | EAS                 |
| OMAChain Mainnet  | 6623     | EAS                 |
| OMAChain Devnet   | 66233    | EAS                 |

The active chain is determined by `NEXT_PUBLIC_ACTIVE_CHAIN`, not the user's wallet.

---

## 6. Per-Project: omatrust-backend

**Repository:** `omatrust-backend`
**Full env reference:** [`.env.example`](https://github.com/oma3dao/omatrust-backend/blob/main/.env.example)

### Domains

| Environment | Preview Phase                 | Target State                 |
|-------------|-------------------------------|------------------------------|
| public-test | `preview.backend.omatrust.org`| `test.backend.omatrust.org`  |
| Production  | 302 → preview                 | `backend.omatrust.org`       |
| Development | —                             | `dev.backend.omatrust.org`   |

### Project-Specific Variables

These are in addition to the common variables in Section 3.

**Self-reference & CORS (values differ per environment):**

| Variable                        | Production                     | public-test                         | Development                         |
|---------------------------------|--------------------------------|-------------------------------------|-------------------------------------|
| `OMATRUST_BACKEND_URL`          | `https://backend.omatrust.org` | `https://test.backend.omatrust.org` | `https://dev.backend.omatrust.org`  |
| `OMATRUST_ALLOWED_SIWE_DOMAINS` | `app.omatrust.org`             | `test.app.omatrust.org`             | `dev.app.omatrust.org`              |
| `OMATRUST_ALLOWED_CORS_ORIGINS` | `https://app.omatrust.org`     | `https://test.app.omatrust.org`     | `https://dev.app.omatrust.org`      |

**Infrastructure (values differ per environment):**

| Variable                  | Production                          | public-test                              | Development                            |
|---------------------------|-------------------------------------|------------------------------------------|----------------------------------------|
| `OMATRUST_PREMIUM_RPC_URL`| `https://rpc.chain.oma3.org/`       | `https://rpc.testnet.chain.oma3.org/`    | `https://rpc.devnet.chain.oma3.org/`   |
| `SUPABASE_URL`            | `<prod-supabase-url>`               | `<testnet-supabase-url>`                 | `<devnet-supabase-url>`                |
| `SUPABASE_SECRET_KEY`     | `<prod-key>`                        | `<testnet-key>`                          | `<devnet-key>`                         |
| `STRIPE_SECRET_KEY`       | `<prod-stripe>`                     | `<test-stripe>`                          | `<test-stripe>`                        |
| `STRIPE_WEBHOOK_SECRET`   | `<prod-webhook>`                    | `<test-webhook>`                         | `<test-webhook>`                       |

### Notes

- The backend shares `THIRDWEB_SECRET_KEY` rotation timing with `rep-attestation-frontend` — rotate both simultaneously during the maintenance window (see Section 8).
- Supabase and Stripe credentials are per-environment. Production should use separate Supabase projects from testnet/devnet.
- `OMATRUST_SESSION_SECRET` must be unique per environment and should never be shared across environments.

---

## 7. Per-Project: omatrust-widgets

**Repository:** `omatrust-widgets`
**Full env reference:** [`.env.example`](https://github.com/oma3dao/omatrust-widgets/blob/main/.env.example)

### Domains

| Environment | Preview Phase                  | Target State                  |
|-------------|--------------------------------|-------------------------------|
| public-test | `preview.widgets.omatrust.org` | `test.widgets.omatrust.org`   |
| Production  | 302 → preview                  | `widgets.omatrust.org`        |
| Development | —                              | `dev.widgets.omatrust.org`    |

### Project-Specific Variables

These are in addition to the common variables in Section 3.

**`NEXT_PUBLIC_RELAY_BASE_URL`** (optional): Defaults to `https://api.omatrust.org`. The widget submits delegated attestations through the API gateway (`/v1/delegated-attest`), which proxies to the rep-attestation-frontend. Only override for local dev.

**`NEXT_PUBLIC_ASSET_PREFIX`** (optional): Only needed if the widget HTML is reverse-proxied through another domain. For the standard iframe embed (where `src` points to `widgets.omatrust.org`), this is unnecessary — the browser resolves assets relative to the iframe's own origin.

### Notes

- No server-side secrets in this project — it's purely a frontend.

---

## 8. Per-Project: omatrust-api-gateway

**Repository:** `omatrust-api-gateway`
**Full env reference:** [`.env.example`](https://github.com/oma3dao/omatrust-api-gateway/blob/main/.env.example)

### Domains

| Environment | Preview Phase                | Target State               |
|-------------|------------------------------|----------------------------|
| public-test | `preview.api.omatrust.org`   | `test.api.omatrust.org`    |
| Production  | 302 → preview                | `api.omatrust.org`         |
| Development | —                            | `dev.api.omatrust.org`     |

### Architecture

```
Client → api.omatrust.org/v1/... → Edge Function proxy → upstream service
```

Each route is a serverless Edge Function (`api/v1/*.ts`) that proxies requests upstream. The active chain determines the upstream prefix via the same `CHAIN_PREFIX_MAP` described in Section 2.

The `vercel.json` rewrite (`/v1/:path*` → `/api/v1/:path*`) maps clean public URLs to the Edge Function file structure.

### Project-Specific Variables

Only the active chain variable is needed (see Section 3). Optional domain overrides exist for local dev but are not set in deployed environments:

| Variable                     | Default                 | Purpose                            |
|------------------------------|-------------------------|------------------------------------|
| `OMATRUST_BACKEND_DOMAIN`    | `backend.omatrust.org`  | Override backend upstream domain    |
| `OMATRUST_REPUTATION_DOMAIN` | `app.omatrust.org`      | Override reputation upstream domain |

### Notes

- This project has no secrets — it's a thin proxy layer.
- The `CHAIN_PREFIX_MAP` in `upstreams.ts` must stay in sync with the one in `rep-attestation-frontend/src/lib/service-urls.ts`.

---

## 9. Security & Key Rotation

### Signing Method by Environment

| Vercel Environment | Signing Method             | Configuration                     |
|--------------------|----------------------------|-----------------------------------|
| Production         | Thirdweb Server Wallet     | Configured via Thirdweb dashboard |
| public-test        | Thirdweb Server Wallet     | Configured via Thirdweb dashboard |
| Development        | `EAS_DELEGATE_PRIVATE_KEY` | Raw private key env var           |
| Preview            | `EAS_DELEGATE_PRIVATE_KEY` | Raw private key env var           |

### Signing Addresses

| Vercel Environment | Signing Address                              |
|--------------------|----------------------------------------------|
| Production         | `0x...` (Thirdweb Server Wallet — mainnet)   |
| public-test        | `0x...` (Thirdweb Server Wallet — testnet)   |
| Development        | `0x...` (raw key — devnet)                   |

### Thirdweb Secret Key Rotation (frequent)

The `THIRDWEB_SECRET_KEY` authenticates API access to the Thirdweb project. It does not change the on-chain signing address. Thirdweb does not support multiple concurrent secret keys, so rotation requires a brief maintenance window.

1. Set `MAINTENANCE_IN_PROGRESS` = `true` in Vercel (both `rep-attestation-frontend` and `omatrust-backend`, applicable environments)
2. Redeploy (apps now return maintenance-mode responses)
3. Rotate the secret key in Thirdweb dashboard (old key is now invalid)
4. Update `THIRDWEB_SECRET_KEY` in Vercel with the new key (all projects that use it)
5. Set `MAINTENANCE_IN_PROGRESS` = `false`
6. Redeploy (apps resume normal operation)
7. Store new key in Bitwarden (replace old value)
8. Verify attestation flow works end-to-end

### Server Wallet Address Rotation (rare)

Only needed if the wallet is compromised or being replaced.

1. Create new server wallet in Thirdweb dashboard
2. Fund new wallet with OMA (see [funding guide](../../oma3-ops/docs/funding-omachain-wallets.md))
3. Update all downstream references:

| #   | File / Location                                                          | What to update                                         |
|-----|--------------------------------------------------------------------------|--------------------------------------------------------|
| 1   | `oma3-ops/oma3-internal-addresses.json`                                  | Wallet address for the relevant role and environment   |
| 2   | `app-registry-frontend/src/config/controller-witness-config.ts`          | `APPROVED_CONTROLLER_WITNESS_ATTESTERS[<chainId>]`     |
| 3   | `omatrust-backend/src/lib/routes/public/trust-anchors.ts`                | `APPROVED_ISSUERS` or `APPROVED_TEST_ISSUERS` entry    |
| 4   | `app-registry-evm-solidity/contract-addresses.txt`                       | Wallet address if recorded there                       |
| 5   | Vercel env vars (`THIRDWEB_SERVER_WALLET_ADDRESS`)                       | All projects, applicable environments                  |
| 6   | On-chain issuer (if wallet is an authorized resolver issuer)             | Timelock proposal: remove old + add new via `oma3-ops` |
| 7   | This document (Signing Addresses table above)                            | Update address                                         |

4. Redeploy affected services
5. If the old wallet was an on-chain issuer, submit timelock proposals to remove old and add new (see `oma3-ops` admin scripts)

### Raw Key Rotation (Development/Preview)

1. Generate new private key
2. Update `EAS_DELEGATE_PRIVATE_KEY` in Vercel
3. Redeploy
4. Update `APPROVED_TEST_ISSUERS` in `omatrust-backend/src/lib/routes/public/trust-anchors.ts`
5. Update this document (Signing Addresses table)

