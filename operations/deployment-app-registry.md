# Deployment Guide: app-registry-frontend

Operational runbook for deploying and configuring the App Registry frontend.

**Policy reference:** [deployment-model.md](./deployment-model.md) — branching model, environment semantics, URL conventions, security principles.

---

## 1. Overview

The App Registry frontend allows users to register and manage tokenized applications on-chain using the ERC-8004 standard.

**Repository:** `app-registry-frontend`

---

## 2. Domains

### Preview Phase (Current)

| Hostname                        | Vercel Environment | Status                   |
|---------------------------------|--------------------|--------------------------|
| `preview.registry.omatrust.org` | public-test        | Active (app serves here) |
| `registry.omatrust.org`         | Production         | 302 redirects to preview |
| `test.registry.omatrust.org`    | —                  | 302 redirects to preview |

### Post-Preview (Target State)

| Hostname                      | Vercel Environment | Target Chain |
|-------------------------------|--------------------|--------------|
| `test.registry.omatrust.org`  | public-test        | Testnet      |
| `registry.omatrust.org`       | Production         | Mainnet      |
| `dev.registry.omatrust.org`   | Development        | Devnet       |

---

## 3. Vercel Environment Configuration

### Preview Phase (Current)

| Setting          | public-test                      | Preview                 | Production                              |
|------------------|----------------------------------|-------------------------|-----------------------------------------|
| Type             | Custom                           | Built-in                | Built-in                                |
| Branch Tracking  | ON → `staging`                   | ON → all branches       | ON → `main`                             |
| Deployment       | Auto-deploy on push to `staging` | Auto on PR              | Auto (302 redirect, app doesn't run)    |
| Domain           | `preview.registry.omatrust.org`  | (Vercel auto-generated) | `registry.omatrust.org` → 302 redirect  |

### Post-Preview (Target State)

| Setting          | Production                 | public-test                    | Development                   | Preview                 |
|------------------|----------------------------|--------------------------------|-------------------------------|-------------------------|
| Type             | Built-in                   | Custom                         | Built-in                      | Built-in                |
| Branch Tracking  | ON → `main`                | OFF                            | ON → `staging`                | ON → all branches       |
| Deployment       | Auto on merge to `main`    | Manual promotion only          | Auto on merge to `staging`    | Auto on PR              |
| Domain           | `registry.omatrust.org`    | `test.registry.omatrust.org`   | `dev.registry.omatrust.org`   | (Vercel auto-generated) |

---

## 4. Environment Variables

### Required Variables

| Variable                         | Purpose                                                    |
|----------------------------------|------------------------------------------------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | Chain identifier (e.g., `omachain-testnet`)                |
| `NEXT_PUBLIC_APP_BASE_URL`       | Canonical URL for this deployment (stored on-chain)        |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | Thirdweb client identifier                                 |
| `NEXT_PUBLIC_DEBUG_ADAPTER`      | Enable debug logging for development                       |

### Signing Variables

| Variable              | Purpose                                                          |
|-----------------------|------------------------------------------------------------------|
| `THIRDWEB_SECRET_KEY` | Server Wallet access (Production/public-test)                    |
| `ISSUER_PRIVATE_KEY`  | Raw private key for controller-witness attestation (dev/preview) |

### Environment Variable Matrix

**Preview Phase (Current):**

| Variable                         | public-test                          | Preview            |
|----------------------------------|--------------------------------------|--------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | `omachain-testnet`                   | `omachain-testnet` |
| `NEXT_PUBLIC_APP_BASE_URL`       | `https://test.registry.omatrust.org` | ❌ Do not set       |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | `<shared>`                           | `<shared>`         |
| `THIRDWEB_SECRET_KEY`            | `<secret>`                           | —                  |
| `ISSUER_PRIVATE_KEY`             | —                                    | `<testnet-key>`    |
| `NEXT_PUBLIC_DEBUG_ADAPTER`      | `true`                               | `true`             |

**Post-Preview (Target State):**

| Variable                         | Production                      | public-test                          | Development                         | Preview            |
|----------------------------------|---------------------------------|--------------------------------------|-------------------------------------|--------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | `omachain-mainnet`              | `omachain-testnet`                   | `omachain-devnet`                   | `omachain-testnet` |
| `NEXT_PUBLIC_APP_BASE_URL`       | `https://registry.omatrust.org` | `https://test.registry.omatrust.org` | `https://dev.registry.omatrust.org` | ❌ Do not set       |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | `<shared>`                      | `<shared>`                           | `<shared>`                          | `<shared>`         |
| `THIRDWEB_SECRET_KEY`            | `<secret>`                      | `<secret>`                           | —                                   | —                  |
| `ISSUER_PRIVATE_KEY`             | —                               | —                                    | `<devnet-key>`                      | `<testnet-key>`    |
| `NEXT_PUBLIC_DEBUG_ADAPTER`      | `false`                         | `false`                              | `true`                              | `true`             |

### NEXT_PUBLIC_APP_BASE_URL

This variable is **not** the URL where the app is served. It is the **canonical URL stored on-chain** in `dataUrl` and `iwpsPortalUri` fields.

- Must point to a permanent URL that will always resolve
- During preview phase: app serves at `preview.registry.omatrust.org`, but on-chain URLs point to `test.registry.omatrust.org` (which redirects to preview)
- **Never set on Preview environment** — ephemeral URLs would break on-chain references

### Optional Contract Overrides

Only needed if overriding default addresses from `src/config/chains.ts`:

- `NEXT_PUBLIC_REGISTRY_ADDRESS`
- `NEXT_PUBLIC_METADATA_ADDRESS`
- `NEXT_PUBLIC_RESOLVER_ADDRESS`

---

## 5. Contract Dependencies

| Contract                | Purpose                                      |
|-------------------------|----------------------------------------------|
| `OMA3AppRegistry`       | ERC-8004 token registry for app registration |
| `OMA3AppMetadata`       | On-chain metadata storage                    |
| `OMA3ResolverWithStore` | DID resolution and attestation storage       |
| EAS                     | Controller-witness attestation               |

Contract addresses are configured in [`src/config/chains.ts`](https://github.com/oma3dao/app-registry-frontend/blob/main/src/config/chains.ts) or via environment variable overrides.

### Supported Chains

| Chain             | Chain ID | Contracts                                    |
|-------------------|----------|----------------------------------------------|
| OMAChain Testnet  | 66238    | Registry, Metadata, Resolver, EAS            |
| OMAChain Mainnet  | 6623     | Registry, Metadata, Resolver, EAS            |
| OMAChain Devnet   | 66233    | Registry, Metadata, Resolver, EAS            |

The active chain is determined by `NEXT_PUBLIC_ACTIVE_CHAIN`, not the user's wallet.

---

## 6. Security

### Signing Method by Environment

| Vercel Environment | Signing Method         | Configuration                     |
|--------------------|------------------------|-----------------------------------|
| Production         | Thirdweb Server Wallet | Configured via Thirdweb dashboard |
| public-test        | Thirdweb Server Wallet | Configured via Thirdweb dashboard |
| Development        | `ISSUER_PRIVATE_KEY`   | Raw private key env var           |
| Preview            | `ISSUER_PRIVATE_KEY`   | Raw private key env var           |

### Signing Addresses

| Vercel Environment | Signing Address                              |
|--------------------|----------------------------------------------|
| Production         | `0x...` (Thirdweb Server Wallet — mainnet)   |
| public-test        | `0x...` (Thirdweb Server Wallet — testnet)   |
| Development        | `0x...` (raw key — devnet)                   |

### Key Rotation

- **Server Wallet (Production/public-test):** Rotate via Thirdweb dashboard. Update signing address in this doc and in `oma3-ops/oma3-internal-addresses.json`.
- **Raw key (Development/Preview):** Generate new key → update `ISSUER_PRIVATE_KEY` in Vercel → redeploy → update this doc.

### Controller Witness Attesters

The attestation wallet must be registered in `src/config/controller-witness-config.ts` → `APPROVED_CONTROLLER_WITNESS_ATTESTERS[<chainId>]`. Update this config whenever the signing wallet changes for an environment.

---

## 7. Pre-Mainnet Redirect

Redirects are configured in **Vercel's Domains UI** (not code or env vars).

| Domain                       | Redirects To                    | Type |
|------------------------------|---------------------------------|------|
| `registry.omatrust.org`      | `preview.registry.omatrust.org` | 302  |
| `test.registry.omatrust.org` | `preview.registry.omatrust.org` | 302  |

### At Mainnet Launch

1. Remove redirects in Vercel Domains UI
2. Attach `registry.omatrust.org` directly to the Production environment
3. Attach `test.registry.omatrust.org` directly to the public-test environment
4. Set `NEXT_PUBLIC_ACTIVE_CHAIN` = `omachain-mainnet` on Production
5. Set `NEXT_PUBLIC_APP_BASE_URL` = `https://registry.omatrust.org` on Production
6. Deploy (merge `staging` → `main`)

---

## 8. Vercel Setup Steps

Steps for setting up this project from scratch in Vercel.

### 1. Create the Vercel Project

Import `app-registry-frontend` from GitHub. Vercel auto-detects Next.js.

### 2. Create `public-test` Environment

1. Project Settings → Environments → Add Environment
2. Name: `public-test`
3. Branch Tracking: ON → `staging`
4. Add domain: `preview.registry.omatrust.org`

### 3. Configure Production Redirect

1. Project Settings → Domains
2. Add `registry.omatrust.org` — configure as redirect to `preview.registry.omatrust.org` (302)
3. Add `test.registry.omatrust.org` — configure as redirect to `preview.registry.omatrust.org` (302)

### 4. Set Environment Variables

Add variables per the matrix in Section 4. Select the correct environment scope for each variable.

### 5. Configure DNS

| Hostname                        | Type  | Value                  |
|---------------------------------|-------|------------------------|
| `preview.registry.omatrust.org` | CNAME | `cname.vercel-dns.com` |
| `registry.omatrust.org`         | CNAME | (already configured)   |
| `test.registry.omatrust.org`    | CNAME | `cname.vercel-dns.com` |

Exact values are provided by Vercel when adding domains.

### 6. Verify Deployment

1. Push to `staging` → confirm auto-deploy to `preview.registry.omatrust.org`
2. Visit `registry.omatrust.org` → confirm 302 redirect to preview
3. Visit `test.registry.omatrust.org` → confirm 302 redirect to preview
4. Test app registration end-to-end on the deployed environment
