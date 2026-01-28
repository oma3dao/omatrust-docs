# OMA3 Deployment Model

This document defines the shared Git, Vercel, and blockchain deployment model for all OMA3 frontends.

**Read this first.** Application-specific deployment guides reference this document for the foundational model.

---

## Current Phase: Developer Preview

We are currently in a **Developer Preview** phase with a single temporary chain (ID 66238). This phase prioritizes speed and iteration over strict controls.

**Preview Phase Characteristics:**
- Single chain (66238) - may become permanent testnet or be deprecated
- Production URLs redirect to preview URLs (e.g., `registry.omatrust.org` → `preview.registry.omatrust.org`)
- `staging` branch auto-deploys to public-test environment
- Direct commits to `staging` are allowed
- `main` branch is PR-only (release discipline maintained)

**Future Phases:**
- Phase 2: Chain migration (when permanent testnet/devnet infrastructure is ready)
- Phase 3: Mainnet launch (three chains: Devnet, Testnet, Mainnet)

---

## 1. Git Branching Model

Branches represent **code maturity**, not infrastructure or blockchains. Branch names must never correspond to blockchain network names.

### Branch Types

| Branch       | Vercel Environment | Auto-Deploy | Purpose                                     |
|--------------|--------------------|-------------|---------------------------------------------|
| `main`       | Production         | Yes         | Production-ready code (redirects during preview) |
| `staging`    | public-test        | Manual*     | Integration branch for development          |
| `feature/*`  | Preview            | Yes         | New functionality, refactors, infra changes |
| `fix/*`      | Preview            | Yes         | Bug fixes, corrections, regressions         |
| `hotfix/*`   | —                  | No          | Emergency production fixes (see below)      |

*During preview phase, `staging` auto-deploys to public-test for fast iteration. Post-preview, promotion will be manual.

### Branch Semantics

**`feature/*`** — Default lane for most work

Use for:
- New functionality
- Refactors
- Infrastructure changes
- CI/test improvements
- Schema changes
- Documentation that affects behavior or guarantees

Mental model: *"This introduces or meaningfully changes behavior."*

If someone hesitates about where something goes → it's a `feature/*`.

**`fix/*`** — Corrective but non-emergency

Use for:
- Bugs
- Correctness issues
- Regressions
- Small behavior fixes

Mental model: *"Something is wrong, but the world is not on fire."*

Still follows normal flow: `fix/*` → `staging` → normal review → normal promotion. The prefix signals intent to reviewers, not a different process.

**`hotfix/*`** — Emergency escape hatch

Use rarely. This branch type exists to prevent panic improvisation when production is broken.

Mechanics:
- Branch from `main` (not `staging`)
- Merge directly into `main` via PR (exception to normal flow, enforced by CI)
- After merging to `main`, also merge into `staging` to prevent drift

Rules:
- Requires higher review bar
- Must be justified in PR description
- Backport to `staging` is mandatory

Mental model: *"Production is broken or unsafe now."*

Even if unused for months, having this defined prevents ad-hoc emergency procedures.

### Standard Flow (Preview Phase)

```
feature/* ─┐
fix/*     ─┼─→ staging → public-test (auto-deploy to preview.*.omatrust.org)
           │                    
           ↓                    
        Preview            main (PR-only, Production redirects to preview)
```

### Standard Flow (Post-Preview)

```
feature/* ─┐
fix/*     ─┼─→ staging → [manual] → public-test → [merge to main] → Production
           ↓
        Preview
```

### Emergency Flow (hotfix only)

```
main → hotfix/* → main (emergency fix)
                ↘ staging (backport to avoid drift)
```

**Notes:**
- `Production`, `Preview`, and `Development` are Vercel's built-in environments (capitalized). `public-test` is a custom environment (lowercase).

**Rules:**
- `main` has production semantics only. Merges to `main` automatically deploy to Production.
- `staging` is the integration branch. All feature and fix work merges here first.
- `feature/*`, `fix/*`, `hotfix/*` branches are ephemeral. Delete after merge.
- Never create branches named `testnet`, `devnet`, `mainnet`, or any blockchain network name.

---

## 2. Main Branch Protections (Production Safety)

The `main` branch has production semantics. Any commit merged into `main` automatically deploys to the Production Vercel environment. Accidental or ad-hoc commits to `main` are a high-risk operational failure and must be mechanically prevented.

### No Direct Commits

Direct pushes to `main` are strictly prohibited.

- No human may push directly to `main`, including repository administrators.
- All changes to `main` must occur via pull request.
- This rule must be enforced by repository branch protection settings, not by convention.

### Pull Request Requirements

Pull requests into `main` must satisfy all of the following:

| Requirement                                   | Rationale                                                            |
|-----------------------------------------------|-----------------------------------------------------==============---|
| Source branch must be `staging` or `hotfix/*` | Ensures code has passed through Development (or is an emergency fix) |
| At least one human approval required.         | Prevents unreviewed code from reaching production                    |
| All CI/status checks must pass                | Ensures automated quality gates are satisfied                        |
| Branch must be up to date with `main`.        | Prevents merge conflicts and stale deployments                       |
| Force pushes disallowed                       | Preserves audit trail and prevents history rewriting                 |

### Source Branch Enforcement

GitHub's branch protection does not natively restrict which source branches can merge into a protected branch. This rule is enforced via a required CI check that validates the PR source branch is either `staging` or matches `hotfix/*`.

This check must be marked as **required** in the repository's branch protection settings. PRs from any other branch will fail CI and cannot be merged.

### Main as a Release Artifact

`main` is not a development branch.

- No one "works on" `main`.
- No one "fixes" `main` directly.
- `main` is the result of a conscious release decision, not an experimentation surface.

Auto-deployment from `main` is acceptable and expected. The safety boundary is the merge into `main`, not the deployment itself. Once code is in `main`, it is production code by definition.

### Complementary Protection Mechanisms

Production safety is enforced at two levels:

| Environment  | Protection Mechanism       | Risk Profile                            |
|--------------|----------------------------|-----------------------------------------|
| public-test  | Manual deployment promotion| External developers depend on stability |
| Production   | Git branch protections     | Real users, real transactions           |

These mechanisms are complementary:
- `public-test` is protected by requiring a human to explicitly promote a build.
- `Production` is protected by requiring a human to explicitly approve a merge.

Both gates exist because the consequences of instability increase with audience size.

---

## 3. Vercel Environments

There is one frontend codebase per application. Vercel environments control audience and stability, not code differences.

### Preview Phase Configuration

| Environment    | Type     | Branch Tracking | Follows Branch | Deployment Method | Domain Pattern              |
|----------------|----------|-----------------|----------------|-------------------|-----------------------------|
| Preview        | Built-in | ON              | `feature/*`    | Automatic on PR   | (Vercel auto-generated)     |
| public-test    | Custom   | ON              | `staging`      | Auto-deploy       | `preview.<app>.omatrust.org`|
| Production     | Built-in | ON              | `main`         | Auto (redirects)  | `<app>.omatrust.org` → 302  |

### Post-Preview Configuration (Target State)

| Environment    | Type     | Branch Tracking | Follows Branch | Deployment Method     | Domain Pattern              |
|----------------|----------|-----------------|----------------|-----------------------|-----------------------------|
| Preview        | Built-in | ON              | `feature/*`    | Automatic on PR       | (Vercel auto-generated)     |
| Development    | Built-in | ON              | `staging`      | Automatic on merge    | `dev.<app>.omatrust.org`    |
| public-test    | Custom   | OFF             | `staging`      | Manual promotion only | `test.<app>.omatrust.org`   |
| Production     | Built-in | ON              | `main`         | Automatic on merge    | `<app>.omatrust.org`        |

### Environment Details

**Preview**
- Vercel's built-in Preview environment
- Automatic for all `feature/*` and `fix/*` branches and pull requests
- Ephemeral deployments for code review
- Targets current chain (66238)

**public-test** (Preview Phase)
- Custom Vercel environment
- Branch Tracking: **ON**, follows `staging` (auto-deploy for fast iteration)
- Domain: `preview.<app>.omatrust.org`
- Targets current chain (66238)
- This is where the app is actively served during preview phase

**public-test** (Post-Preview)
- Branch Tracking: **OFF**
- Deployments via manual promotion only
- Domain: `test.<app>.omatrust.org`
- Targets Testnet

**Production** (Preview Phase)
- Domain redirects to `preview.<app>.omatrust.org` via Vercel 302 redirect
- App doesn't actually run here during preview
- Branch protections still enforced on `main`

**Production** (Post-Preview)
- Automatically deploys when code is merged to `main`
- Targets Mainnet blockchain

---

## 4. Chain Rollout Timeline

### Current State: Developer Preview
- One chain: Chain ID 66238 (temporary, may become permanent testnet)
- All environments target this single chain
- Production URLs redirect to preview URLs

### Phase 2: Chain Migration
- Current chain either becomes permanent testnet OR is replaced and deprecated
- New permanent Testnet launches (Chain ID 66238 reused)
- New Devnet launches (Chain ID 66233)
- Two chains operational

### Phase 3: Mainnet Launch
- Mainnet launches (Chain ID 6623)
- Three chains operational: Devnet, Testnet, Mainnet
- Production serves mainnet directly (no redirect)

---

## 5. Blockchain Configuration

There is a **single frontend codebase** per application. Each deployment targets exactly one blockchain via environment variables. Blockchain selection is pure configuration—never a branch or code fork.

### Common Environment Variables

| Variable                         | Purpose                                                           |
|----------------------------------|-------------------------------------------------------------------|
| `NEXT_PUBLIC_ACTIVE_CHAIN`       | Chain identifier (e.g., `omachain-testnet`)                       |
| `NEXT_PUBLIC_THIRDWEB_CLIENT_ID` | Thirdweb client identifier                                        |
| `NEXT_PUBLIC_DEBUG_ADAPTER`      | Enable debug logging (`true` for dev environments)                |
| `REDIRECT_PRODUCTION`            | Enable pre-mainnet redirect (`true` only in Production)           |
| `REDIRECT_TARGET`                | Target URL for redirect (e.g., `https://test.<app>.omatrust.org`) |

See [Section 7: Pre-Mainnet Redirect Policy](#7-pre-mainnet-redirect-policy) for details on `REDIRECT_PRODUCTION` and `REDIRECT_TARGET`.

Application-specific variables (contract addresses, signing keys, etc.) are documented in each application's deployment guide.

---

## 6. URL and Domain Conventions

### Preview Phase Domains

| Hostname Pattern             | Vercel Environment | Status                    |
|------------------------------|--------------------|---------------------------|
| `preview.<app>.omatrust.org` | public-test        | Active (app serves here)  |
| `<app>.omatrust.org`         | Production         | 302 redirects to preview  |
| `test.<app>.omatrust.org`    | —                  | 302 redirects to preview  |

### Post-Preview Domains (Target State)

| Hostname Pattern             | Vercel Environment | Target Chain |
|------------------------------|--------------------|--------------|
| `dev.<app>.omatrust.org`     | Development        | Devnet       |
| `test.<app>.omatrust.org`    | public-test        | Testnet      |
| `<app>.omatrust.org`         | Production         | Mainnet      |

**Rules:**
- Testnet and Devnet must always be identifiable from the hostname (`dev.*`, `test.*`, or `preview.*`).
- Production hostnames must never silently point to non-mainnet chains. Visible HTTP redirects (302) are acceptable during preview phase.
- The blockchain a user is interacting with must be unambiguous from the URL.

---

## 7. Preview Phase Redirect Policy

During the Developer Preview phase, production URLs redirect to preview URLs. This ensures users see the `preview.*` hostname and understand they're on a temporary/test environment.

### Implementation

Redirects are configured in **Vercel's Domains UI**, not via environment variables or code:

1. Go to Project Settings → Domains
2. Add the production domain (e.g., `registry.omatrust.org`)
3. Configure as redirect to preview URL (e.g., `preview.registry.omatrust.org`)
4. Select **302 (Temporary Redirect)**

### Current Redirect Configuration

| Production Domain           | Redirects To                          | Type |
|-----------------------------|---------------------------------------|------|
| `registry.omatrust.org`     | `preview.registry.omatrust.org`       | 302  |
| `test.registry.omatrust.org`| `preview.registry.omatrust.org`       | 302  |
| `reputation.omatrust.org`   | `preview.reputation.omatrust.org`     | 302  |

### At Mainnet Launch

When Mainnet is ready:
1. Remove the redirect in Vercel Domains UI
2. Attach the production domain directly to the Production environment
3. Update `NEXT_PUBLIC_ACTIVE_CHAIN` to target Mainnet
4. Deploy as usual (merge `staging` → `main`)

### Why Vercel Domain Redirects (Not Code)

- Simpler: no environment variables or code logic needed
- Visible: redirect happens before app code runs
- Auditable: configuration is in Vercel UI, not buried in code
- Clean: no `REDIRECT_PRODUCTION` / `REDIRECT_TARGET` env vars to manage

---

## 8. Vercel Setup Steps (Per Application)

### Create Custom Environments

1. Go to Project Settings → Environments
2. Click "Add Environment"
3. Configure `Development` environment:
   - Enable Branch Tracking
   - Set branch pattern: `staging`
4. Create `public-test` environment:
   - **Disable** Branch Tracking
   - Deployments via manual promotion only

### Set Environment Variables

For each environment:
1. Go to Project Settings → Environment Variables
2. Add variables per the application-specific deployment guide
3. Select which environments each variable applies to
4. Use different values per environment where noted

### Configure Custom Domains

1. Go to Project Settings → Domains
2. Add custom domains per the application-specific deployment guide
3. Configure DNS records as instructed by Vercel

---

## 9. Security

### Signing Key Management

Server-side signing (for attestations, delegated transactions, etc.) uses different mechanisms depending on the phase and environment:

**Preview Phase (Current):**

| Environment        | Signing Method           | Configuration                     |
|--------------------|--------------------------|-----------------------------------|
| public-test        | Raw private key env var  | `ISSUER_PRIVATE_KEY` or `EAS_DELEGATE_PRIVATE_KEY` |
| Preview            | Raw private key env var  | Same as above                     |

**Post-Preview (Target State):**

| Environment        | Signing Method           | Configuration                     |
|--------------------|--------------------------|-----------------------------------|
| Production         | Thirdweb Server Wallet   | Configured via Thirdweb dashboard |
| public-test        | Thirdweb Server Wallet   | Configured via Thirdweb dashboard |
| Development        | Raw private key env var  | e.g., `ISSUER_PRIVATE_KEY`        |
| Preview            | Raw private key env var  | e.g., `ISSUER_PRIVATE_KEY`        |

**Why raw keys during preview:**
- Faster iteration, simpler setup
- Server Wallet migration happens when approaching mainnet (Phase 2)

**Why Server Wallets for production:**
- No raw private keys in environment variables
- Key management, rotation, and audit logging via Thirdweb

### Separate Keys Per Environment

Each environment **must** have its own signing keys/wallets. Never use production wallets on Development environments.

### Key Rotation

**For Development/Preview (raw private key):**
1. Generate new key
2. Update environment variable in Vercel
3. Redeploy
4. Update documentation with new address

**For Production/public-test (Thirdweb Server Wallet):**
1. Rotate via Thirdweb dashboard
2. Update documentation with new address

Application-specific signing key details are documented in each application's deployment guide.
