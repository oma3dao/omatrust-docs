# Deployment Guide: app-registry-frontend

This document covers application-specific deployment details for the OMA3 App Registry frontend.

**Prerequisites:** Read [deployment-model.md](./deployment-model.md) first for the shared branching, environment, and promotion model.

---

## 1. Overview

The App Registry frontend allows users to register and manage tokenized applications on-chain using the ERC-8004 standard.

**Repository:** `app-registry-frontend`

---

## 2. Domains

### Preview Phase (Current)

| Hostname                         | Vercel Environment | Status                   |
|----------------------------------|--------------------|--------------------------|
| `preview.registry.omatrust.org`  | public-test        | Active (app serves here) |
| `registry.omatrust.org`          | Production         | 302 redirects to preview |
| `test.registry.omatrust.org`     | —                  | 302 redirects to preview |

### Post-Preview (Target State)

| Hostname                       | Vercel Environment | Target Chain |
|--------------------------------|--------------------|--------------|
| `dev.registry.omatrust.org`    | Development        | Devnet       |
| `test.registry.omatrust.org`   | public-test        | Testnet      |
| `registry.omatrust.org`        | Production         | Mainnet      |

---

## 3. Environment Variables

### Required Variables

| Variable                         | Purpose                                                                            |
|----------------------------------|------------------------------------------------------------------------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | Chain identifier (e.g., `omachain-mainnet`, `omachain-testnet`, `omachain-devnet`) |
| `NEXT_PUBLIC_APP_BASE_URL`       | Canonical URL for this deployment (stored on-chain)                                |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | Thirdweb client identifier                                                         |
| `ISSUER_PRIVATE_KEY`             | Server-side signing key for attestations                                           |

### Environment Variable Matrix

**Preview Phase (Current):**

| Variable                         | public-test                          | Preview            |
|----------------------------------|--------------------------------------|--------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | `omachain-testnet`                   | `omachain-testnet` |
| `NEXT_PUBLIC_APP_BASE_URL`       | `https://test.registry.omatrust.org` | ❌ Do not set       |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | `<shared>`                           | `<shared>`         |
| `ISSUER_PRIVATE_KEY`             | `<testnet-key>`                      | `<testnet-key>`    |
| `NEXT_PUBLIC_DEBUG_ADAPTER`      | `true`                               | `true`             |

**Post-Preview (Target State):**

| Variable                         | Production                        | public-test                          | Development                         | Preview            |
|----------------------------------|-----------------------------------|--------------------------------------|-------------------------------------|--------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | `omachain-mainnet`                | `omachain-testnet`                   | `omachain-devnet`                   | `omachain-testnet` |
| `NEXT_PUBLIC_APP_BASE_URL`       | `https://registry.omatrust.org`   | `https://test.registry.omatrust.org` | `https://dev.registry.omatrust.org` | ❌ Do not set       |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | `<shared>`                        | `<shared>`                           | `<shared>`                          | `<shared>`         |
| `THIRDWEB_SECRET_KEY`            | `<secret>`                        | `<secret>`                           | —                                   | —                  |
| `ISSUER_PRIVATE_KEY`             | —                                 | —                                    | `<devnet-key>`                      | `<testnet-key>`    |
| `NEXT_PUBLIC_DEBUG_ADAPTER`      | `false`                           | `false`                              | `true`                              | `true`             |

### Optional Contract Overrides

Only needed if overriding default addresses from `src/config/chains.ts`:

- `NEXT_PUBLIC_REGISTRY_ADDRESS`
- `NEXT_PUBLIC_METADATA_ADDRESS`
- `NEXT_PUBLIC_RESOLVER_ADDRESS`

### Preview Deployments Warning

**Do NOT set `NEXT_PUBLIC_APP_BASE_URL` for Preview environments.**

Preview URLs are ephemeral (e.g., `my-branch-abc123.vercel.app`). If this URL is stored on-chain in `dataUrl` or `iwpsPortalUri`, it will break when the preview deployment is deleted.

---

## 4. Contract Dependencies

This frontend interacts with the following contracts:

| Contract                  | Purpose                                      |
|---------------------------|----------------------------------------------|
| `OMA3AppRegistry`         | ERC-8004 token registry for app registration |
| `OMA3AppMetadata`         | On-chain metadata storage                    |
| `OMA3ResolverWithStore`   | DID resolution and attestation storage       |

Contract addresses are configured in `src/config/chains.ts` or via environment variable overrides.

---

## 5. Security

### Issuer Key Management

Each environment **must** have its own `ISSUER_PRIVATE_KEY`. Never use mainnet keys on public-test/Development.

| Vercel Environment | Issuer Address |
|--------------------|----------------|
| Production         | `0x...`        |
| public-test        | `0x...`        |
| Development        | `0x...`        |

### Key Rotation

Key rotation procedures are documented internally and executed according to OMA3 operational policy.

---

## 6. Pre-Mainnet Redirect

During preview phase, production URLs redirect to preview URLs via Vercel's Domains UI (302 redirects).

| Domain                         | Redirects To                        |
|--------------------------------|-------------------------------------|
| `registry.omatrust.org`        | `preview.registry.omatrust.org`     |
| `test.registry.omatrust.org`   | `preview.registry.omatrust.org`     |

At Mainnet launch: remove redirects in Vercel Domains UI and attach domains directly to their respective environments.
