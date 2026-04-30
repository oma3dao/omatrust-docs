# OMATrust

**A Universal Trust Layer for the Open Internet**

OMATrust is a decentralized verification protocol that enables developers, auditors, and users to create machine-verifiable trust signals for apps, APIs, websites, and smart contracts. Built by [OMA3](https://oma3.org), a Swiss non-profit consortium, OMATrust provides the foundation for a verifiable, AI-ready internet.

## License and Participation

- [License terms](./LICENSE)
- Contributor terms are defined in [CONTRIBUTING.md](./CONTRIBUTING.md)

## Core Documents

- **[Whitepaper](./whitepaper/omatrust-whitepaper.md)** - Vision, architecture, and economic model
- **[Specification](./specification/omatrust-specification.md)** - Complete technical specification for OMATrust protocol
- **[PDF Releases](./releases/)** - Downloadable PDF versions of all documents

## Document Management

- **[Document Workflow Guide](./DOCUMENT_WORKFLOW.md)** - Complete process for managing documents from Google Docs to GitHub

## Repository Ecosystem

OMATrust follows a **modular architecture** where each repository serves a specific function:

| Repository                                                                                                 | Purpose                                                        | Status     |
| ---------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------- | ---------- |
| **[omatrust-docs](https://github.com/oma3dao/omatrust-docs)**                                              | Whitepaper, specification, and protocol documentation          | Active     |
| **[developer-docs](https://github.com/oma3dao/developer-docs)**                                            | Developer-facing documentation site (Docusaurus)               | Active     |
| **[app-registry-frontend](https://github.com/oma3dao/app-registry-frontend)**                              | Web interface for registering applications                     | Active     |
| **[rep-attestation-frontend](https://github.com/oma3dao/rep-attestation-frontend)**                        | Web interface for submitting and viewing attestations          | Active     |
| **[omatrust-backend](https://github.com/oma3dao/omatrust-backend)**                                        | Backend service for account, subscription, and delegated flows | Active     |
| **[omatrust-api-gateway](https://github.com/oma3dao/omatrust-api-gateway)**                                | API gateway for OMATrust services (Vercel serverless)          | Active     |
| **[omatrust-widgets](https://github.com/oma3dao/omatrust-widgets)**                                        | Embeddable trust signal components                             | Active     |
| **[omatrust-sdk](https://github.com/oma3dao/omatrust-sdk)**                                                | Framework-agnostic TypeScript SDK (`@oma3/omatrust`)           | Active     |
| **[omatrust-landing](https://github.com/oma3dao/omatrust-landing)**                                        | Public marketing / landing page                                | Active     |
| **[app-registry-evm-solidity](https://github.com/oma3dao/app-registry-evm-solidity)**                      | Smart contracts for app registration and tokenization          | Active     |
| **[rep-attestation-tools-evm-solidity](https://github.com/oma3dao/rep-attestation-tools-evm-solidity)**    | Attestation schemas and tools for reputation system            | Active     |
| **[oma3-ops](https://github.com/oma3dao/oma3-ops)**                                                        | Operational tooling, deployment scripts, governance config     | Active     |

### Deprecated Repositories

| Repository                   | Reason                                                                           |
| ---------------------------- | -------------------------------------------------------------------------------- |
| `app-metadata-evm-solidity`  | Legacy metadata contract. Functionality merged into `app-registry-evm-solidity`. |

### How They Work Together

**Registration**: Developers use the `app-registry-frontend` to register applications, which creates tokens via `app-registry-evm-solidity` contracts.

**Reputation**: Users and auditors submit reviews/certifications through `rep-attestation-frontend`, using schemas from `rep-attestation-tools-evm-solidity`.

**Backend**: The `omatrust-backend` handles account management, subscriptions, and delegated attestation flows. The `omatrust-api-gateway` routes API calls to backend services.

**SDK & Widgets**: The `omatrust-sdk` provides a TypeScript SDK for integrating OMATrust into any application. The `omatrust-widgets` package provides embeddable UI components for displaying trust signals.

**Verification**: Applications can query both systems to display trust signals to users.

**Governance**: Branch protection rulesets and CI configuration are managed centrally in `oma3-ops/github-governance/`.


## Getting Started

### For App Developers
**Register your applications for verification:**
- Apps include websites, binaries, mobile apps, APIs, MCP servers, and even agents (see the specification for details)
- Visit [registry.omatrust.org](https://registry.omatrust.org) to tokenize your app, API, or website
- Review the [registry frontend repository](https://github.com/oma3dao/app-registry-frontend) for integration examples
- See the [Specification](./specification/omatrust-specification.md) for supported application types and metadata requirements
- If you work with an audit firm (SOC 2, pen testing, etc.), encourage them to participate in OMATrust to make your audits verifiable and discoverable

**Integrate review prompts into your applications:**
- Embed OMATrust review widgets or SDK tools to encourage user endorsements
- *Integration tools and SDKs coming soon*

### For Client Developers  
**Integrate trust information into your clients:**
- Query OMATrust to verify legitimacy before using a service
- Retrieve attestations and reviews for trust scoring
- *Tooling and SDKs for client integration coming soon*

### For Auditors
Review [attestation schemas](https://github.com/oma3dao/rep-attestation-tools-evm-solidity/tree/main/schemas-json) and use the [reputation frontend](https://github.com/oma3dao/rep-attestation-frontend) to publish cryptographic attestations.

### For Users  
Visit verify.omatrust.org to search verified applications, submit reviews, and verify website legitimacy.
- *Coming soon*

## Status

**Current Phase**: Pre-launch development
- Core contracts implemented
- Specification complete
- Frontend applications functional
- OMAChain deployment in progress
- Public launch: Q4 2025

## Contributing

OMATrust is open source and welcomes contributions:

1. **Join OMA3** - [Become a consortium member](https://oma3.org/join) to participate in governance and create standards
2. **Submit Issues** - Report bugs or suggest improvements in relevant repositories  
3. **Contribute Code** - Follow contribution guidelines in each repository
4. **Provide Feedback** - Share thoughts on specifications and protocols

## Connect

- **Documentation**: [docs.omatrust.org](https://docs.oma3.org)
- **Twitter**: [@oma3dao](https://twitter.com/oma3dao)
- **LinkedIn**: [Home Page](https://www.linkedin.com/company/oma3/)

## License

Documents in this repository are licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
Code repositories have their own licenses - see individual repositories for details.

---

*Building the trust layer of the open internet.*