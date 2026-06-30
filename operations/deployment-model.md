# OMA3 Deployment Model

Policy and principles for deploying OMATrust systems. This document defines the shared rules for frontend deployment (branching, environment semantics, URL conventions, security boundaries) and serves as the entry point for all deployment documentation. Smart contract deployment follows a separate model — one-time Hardhat deploys with ownership transfer to a TimelockController, documented in the linked guides below.

**Frontend operational runbooks** (domains, env vars, Vercel setup):
- [deployment-rep-attestation.md](./deployment-rep-attestation.md) — OMATrust portal (https://app.omatrust.org)

**Smart contract deployment and admin operations:**
- [app-registry-evm-solidity/tasks/deploy/README.md](../../app-registry-evm-solidity/tasks/deploy/README.md) — All contract deployment (Hardhat/Solidity)
- [oma3-ops/README.md](../../oma3-ops/README.md) — Ongoing on-chain admin operations
- [oma3-ops/docs/funding-omachain-wallets.md](../../oma3-ops/docs/funding-omachain-wallets.md) — Wallet funding and bridging

---

## Current Phase: Developer Preview

Single Testnet chain (ID 66238). This phase mimics Mainnet as closely as possible to prepare for launch.

- Production URLs redirect to preview URLs (e.g., `app.omatrust.org` → `preview.app.omatrust.org`)
- `staging` branch auto-deploys to public-test environment
- Direct commits to `staging` are allowed
- `main` branch is PR-only (release discipline maintained)

**Future Phases:**
- Phase 2: Mainnet launch (two chains: Testnet, Mainnet)
- Phase 3: Devnet launch (three chains: Devnet, Testnet, Mainnet)

---

## 1. Git Branching Model

Branches represent **code maturity**, not infrastructure or blockchains. Branch names must never correspond to blockchain network names.

### Branch Types

| Branch      | Vercel Environment | Auto-Deploy | Purpose                                          |
|-------------|--------------------| ------------|--------------------------------------------------|
| `main`      | Production         | Yes         | Production-ready code (redirects during preview) |
| `staging`   | public-test        | Yes*        | Integration branch for development               |
| `feat/*`    | Preview            | Yes         | New functionality, refactors, infra changes      |
| `fix/*`     | Preview            | Yes         | Bug fixes, corrections, regressions              |
| `hotfix/*`  | —                  | No          | Emergency production fixes (see below)           |

*During preview phase, `staging` auto-deploys. Post-preview, promotion will be manual.

### Branch Semantics

**`feat/*`** — Default lane for most work. New functionality, refactors, infrastructure, CI/test improvements, schema changes, documentation that affects behavior. Mental model: *"This introduces or meaningfully changes behavior."* If unsure → `feat/*`.

**`fix/*`** — Corrective but non-emergency. Bugs, correctness issues, regressions. Still follows normal flow: `fix/*` → `staging` → review → promotion. The prefix signals intent, not a different process.

**`hotfix/*`** — Emergency escape hatch. Branch from `main`, merge directly into `main` via PR, then backport to `staging`. Requires higher review bar and justification in PR description.

### Standard Flow (Preview Phase)

```
feat/* ─┐
fix/*  ─┼─→ staging → public-test (auto-deploy to preview.*.omatrust.org)
        ↓
     Preview            main (PR-only, Production redirects to preview)
```

During preview, `staging` auto-deploys to public-test. `main` deploys to Production but Production only serves a 302 redirect — the app doesn't actually run there.

### Standard Flow (Post-Preview)

```
feat/* ─┐
fix/*  ─┼─→ staging → [manual promotion] → public-test (Testnet)
        ↓                                         ↓
     Preview                              [merge to main] → Production (Mainnet)
```

Post-preview, public-test promotion is manual (no auto-deploy). Merging `staging` → `main` triggers auto-deploy to Production, which now serves Mainnet directly.

### Emergency Flow

```
main → hotfix/* → main (emergency fix)
                ↘ staging (mandatory backport)
```

### Rules

- `main` has production semantics. Merges to `main` deploy to Production.
- `staging` is the integration branch. All feature/fix work merges here first.
- `feat/*`, `fix/*`, `hotfix/*` are ephemeral. Delete after merge.
- Never create branches named `testnet`, `devnet`, `mainnet`, or any blockchain network name.

---

## 2. Main Branch Protections

Any commit merged into `main` automatically deploys to Production. Accidental commits to `main` are a high-risk operational failure.

### Rules

- No direct pushes to `main` — enforced by branch protection settings, not convention.
- All changes via pull request.
- Source branch must be `staging` or `hotfix/*` (enforced via CI check).
- At least one human approval required.
- All CI/status checks must pass.
- Branch must be up to date with `main`.
- Force pushes disallowed.

### Source Branch Enforcement

GitHub branch protection does not natively restrict source branches. This is enforced via a required CI check that validates the PR source branch. PRs from other branches fail CI and cannot merge.

### Main as a Release Artifact

`main` is not a development branch. No one works on `main` directly. It is the result of a conscious release decision. The safety boundary is the merge, not the deployment.

---

## 3. Environment Semantics

One frontend codebase per application. Vercel environments control audience and stability.

| Environment | Type     | Purpose                                                   |
|-------------|----------|-----------------------------------------------------------|
| Preview     | Built-in | Ephemeral deployments for code review (auto on PRs)       |
| public-test | Custom   | Externally-visible test environment (preview or testnet)  |
| Production  | Built-in | Live production deployment (mainnet when launched)        |

**Notes:**
- `Production`, `Preview`, and `Development` are Vercel's built-in environments (capitalized).
- `public-test` is a custom environment (lowercase).
- Post-preview, a `Development` environment may be added for devnet.

Specific environment configuration (branch tracking, deployment method, domains, env vars) is defined in each application's deployment guide.

---

## 4. URL and Domain Conventions

### Rules

- Testnet and Devnet must always be identifiable from the hostname (`dev.*`, `test.*`, or `preview.*`).
- Production hostnames must never silently point to non-mainnet chains. Visible HTTP redirects (302) are acceptable during preview phase.
- The blockchain a user is interacting with must be unambiguous from the URL.

### Naming Pattern

| Environment | Pattern                        | Example                     |
|-------------|--------------------------------|-----------------------------|
| Preview     | `preview.<app>.omatrust.org`   | `preview.app.omatrust.org`  |
| Testnet     | `test.<app>.omatrust.org`      | `test.app.omatrust.org`     |
| Devnet      | `dev.<app>.omatrust.org`       | `dev.app.omatrust.org`      |
| Production  | `<app>.omatrust.org`           | `app.omatrust.org`          |

---

## 5. Blockchain Configuration

Each deployment targets exactly one blockchain via environment variables. Blockchain selection is pure configuration — never a branch or code fork.

### Common Environment Variables

| Variable                         | Purpose                                                    |
|----------------------------------|------------------------------------------------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | Chain identifier (e.g., `omachain-testnet`)                |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | Thirdweb client identifier                                 |
| `NEXT_PUBLIC_DEBUG_ADAPTER`      | Enable debug logging (`true` for dev environments)         |

Application-specific variables (contract addresses, signing keys, base URLs) are documented in each application's deployment guide.

### Chain Rollout

| Phase            | Chains                           |
|------------------|----------------------------------|
| Developer Preview| Testnet (66238) only             |
| Phase 2          | Testnet (66238) + Mainnet (6623) |
| Phase 3          | Devnet + Testnet + Mainnet       |

---

## 6. Pre-Mainnet Redirect Policy

During Developer Preview, production URLs redirect to preview URLs via **Vercel's Domains UI** (not code or environment variables).

### Why Vercel Domain Redirects

- Simpler: no environment variables or code logic
- Visible: redirect happens before app code runs
- Auditable: configuration in Vercel UI, not buried in code
- Clean: no redirect-related env vars to manage

### At Mainnet Launch

1. Remove the redirect in Vercel Domains UI
2. Attach the production domain directly to the Production environment
3. Update `NEXT_PUBLIC_ACTIVE_CHAIN` to target Mainnet
4. Deploy (merge `staging` → `main`)

Specific redirect configurations are documented in each application's deployment guide.

---

## 7. Security Principles

### Signing Key Strategy

| Environment        | Signing Method         | Rationale                                        |
|--------------------|------------------------|--------------------------------------------------|
| Production         | Thirdweb Server Wallet | No raw keys in env vars; audit logging; rotation |
| public-test        | Thirdweb Server Wallet | Matches production security model                |
| Development        | Raw private key        | Faster iteration, simpler setup                  |
| Preview            | Raw private key        | Ephemeral, developer-only                        |

### Rules

- Each environment **must** have its own signing keys/wallets. Never reuse production wallets on dev environments.
- Server Wallet migration happens when approaching mainnet (Phase 2).
- Application-specific signing keys and addresses are documented in each application's deployment guide.

### Key Rotation

- **Raw private key:** Generate new key → update env var in Vercel → redeploy → update docs.
- **Thirdweb Server Wallet:** Rotate via Thirdweb dashboard → update docs.
