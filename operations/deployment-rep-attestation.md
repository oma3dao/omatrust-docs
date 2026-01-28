# Deployment Guide: rep-attestation-frontend

This document covers application-specific deployment details for the OMA3 Reputation Attestation frontend.

**Prerequisites:** Read [deployment-model.md](./deployment-model.md) first for the shared branching, environment, and promotion model.

---

## 1. Overview

The Reputation Attestation frontend allows users to create and verify attestations using the Ethereum Attestation Service (EAS).

**Repository:** `rep-attestation-frontend`

**Launch Priority:** This frontend launches before app-registry-frontend.

---

## 2. Domains

### Preview Phase (Current)

| Hostname                           | Vercel Environment | Status                   |
|------------------------------------|--------------------|--------------------------|
| `preview.reputation.omatrust.org`  | public-test        | Active (app serves here) |
| `reputation.omatrust.org`          | Production         | 302 redirects to preview |

### Post-Preview (Target State)

| Hostname                          | Vercel Environment | Target Chain |
|-----------------------------------|--------------------|--------------|
| `dev.reputation.omatrust.org`     | Development        | Devnet       |
| `test.reputation.omatrust.org`    | public-test        | Testnet      |
| `reputation.omatrust.org`         | Production         | Mainnet      |

---

## 3. Environment Variables

### Required Variables

| Variable                         | Purpose                                            |
|----------------------------------|----------------------------------------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | Chain identifier for the primary attestation chain |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | Thirdweb client identifier                         |

### Server-Side Signing

Delegated attestation signing uses different mechanisms depending on the environment:

| Vercel Environment | Signing Method             | Configuration                     |
|--------------------|------------------------==--|-----------------------------------|
| Production         | Thirdweb Server Wallet     | Configured via Thirdweb dashboard |
| public-test        | Thirdweb Server Wallet     | Configured via Thirdweb dashboard |
| Development        | `EAS_DELEGATE_PRIVATE_KEY` | Raw private key env var           |
| Preview            | `EAS_DELEGATE_PRIVATE_KEY` | Raw private key env var           |

### Environment Variable Matrix

**Preview Phase (Current):**

| Variable                         | public-test        | Preview            |
|----------------------------------|--------------------|--------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | `omachain-testnet` | `omachain-testnet` |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | `<shared>`         | `<shared>`         |
| `EAS_DELEGATE_PRIVATE_KEY`       | `<testnet-key>`    | `<testnet-key>`    |
| `THIRDWEB_SECRET_KEY`            | `<secret>`         | —                  |
| `NEXT_PUBLIC_DEBUG_ADAPTER`      | `true`             | `true`             |

**Post-Preview (Target State):**

| Variable                         | Production         | public-test        | Development        | Preview            |
|----------------------------------|--------------------|--------------------|--------------------|--------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | `omachain-mainnet` | `omachain-testnet` | `omachain-devnet`  | `omachain-testnet` |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | `<shared>`         | `<shared>`         | `<shared>`         | `<shared>`         |
| `THIRDWEB_SECRET_KEY`            | `<secret>`         | `<secret>`         | —                  | —                  |
| `EAS_DELEGATE_PRIVATE_KEY`       | —                  | —                  | `<devnet-key>`     | `<testnet-key>`    |
| `NEXT_PUBLIC_DEBUG_ADAPTER`      | `false`            | `false`            | `true`             | `true`             |

### Notes

- This frontend does **not** use `NEXT_PUBLIC_APP_BASE_URL` since it doesn't store URLs on-chain.
- Production and public-test are expected to use Thirdweb Server Wallets for signing (no raw private keys in env vars).
- Development and Preview use `EAS_DELEGATE_PRIVATE_KEY` for convenience during development.

---

## 4. Supported Chains

This frontend supports multiple chains for attestations:

| Chain              | Chain ID   | Attestation Service |
|--------------------|------------|---------------------|
| OMAchain Devnet    | 66233      | EAS                 |
| OMAchain Testnet   | 66238      | EAS                 |
| OMAchain Mainnet   | 6623       | EAS                 |

The active chain is determined by `NEXT_PUBLIC_ACTIVE_CHAIN`, not the user's wallet.

---

## 5. Contract Dependencies

This frontend interacts with the following contracts:

| Contract       | Purpose                               |
|----------------|---------------------------------------|
| EAS            | Attestation creation and verification |
| SchemaRegistry | Schema registration and lookup        |

Contract addresses are configured in [`src/config/chains.ts`](https://github.com/oma3dao/rep-attestation-frontend/blob/main/src/config/chains.ts):

---

## 6. Security

See [deployment-model.md](./deployment-model.md#9-security) for the shared security model.

### Signing Addresses

| Vercel Environment | Signing Address    |
|--------------------|--------------------|
| Production         | `0x...` (Thirdweb) |
| public-test        | `0x...` (Thirdweb) |
| Development        | `0x...`            |

---

## 7. Pre-Mainnet Redirect

During preview phase, production URL redirects to preview URL via Vercel's Domains UI (302 redirect).

| Domain                       | Redirects To                         |
|------------------------------|--------------------------------------|
| `reputation.omatrust.org`    | `preview.reputation.omatrust.org`    |

At Mainnet launch: remove redirect in Vercel Domains UI and attach domain directly to Production environment.
