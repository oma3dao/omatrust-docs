# OMATrust

**A Universal Trust Layer for the Open Internet**

OMATrust is a decentralized verification protocol that enables developers, auditors, and users to create machine-verifiable trust signals for apps, APIs, websites, and smart contracts. Built by [OMA3](https://oma3.org), a Swiss non-profit consortium, OMATrust provides the foundation for a verifiable, AI-ready internet.

## 📖 Core Documents

- **[Specification](./specification.md)** - Complete technical specification for OMATrust protocol
- **[Whitepaper](./whitepaper.md)** - Vision, architecture, and economic model
- **[PDF Releases](./releases/)** - Downloadable PDF versions of all documents

## 🌐 The Problem

Today's internet lacks a universal trust layer. Users and AI agents have no reliable way to verify:
- Whether an API is legitimate before making micropayments
- If a website is secure before entering sensitive data  
- Whether security audits are authentic and current
- If user reviews are genuine or manipulated

Existing solutions are fragmented: SSL certificates only prove domain ownership, audits live in unverifiable PDFs, and reputation is siloed inside centralized platforms.

## ✨ The Solution

OMATrust creates **machine-verifiable trust** through:

### 🏪 **App Registry**
- Tokenize apps, APIs, websites as NFTs with persistent onchain identity
- Cross-chain compatibility (Ethereum, Solana, Cosmos, etc.)
- Canonical uniqueness enforced via deduplicator contracts

### 🛡️ **Cryptographic Attestations**
- Security audits, compliance certifications, user reviews as verifiable proofs
- Published by independent auditors and certification bodies
- Leverages existing standards (EAS) with DID-native extensions

### 🔍 **Instant Verification**
- Real-time queries for legitimacy before engaging with services
- Programmatic trust decisions for AI agents and automated systems
- No more relying on PDFs, badges, or unverifiable claims

## 🏗️ Architecture

OMATrust operates as a **two-layer system**:

1. **OMATrust Protocol** - Cross-chain verification layer
2. **OMAChain** - Ethereum L2 providing canonical coordination, composability, and subsidized transactions

This design ensures global interoperability while maintaining practical deployment and user experience.

## 🚀 Repository Ecosystem

The OMATrust ecosystem spans multiple specialized repositories:

| Repository | Description | Status |
|------------|-------------|---------|
| **[omatrust-registry-contracts](../app-registry-evm-solidity/)** | Core registry smart contracts | ✅ Active |
| **[omatrust-reputation-schemas](../rep-attestation-tools-evm-solidity/)** | Attestation schemas and tools | ✅ Active |
| **[omatrust-registry-frontend](../app-registry-frontend/)** | Registry web interface | ✅ Active |
| **[omatrust-reputation-frontend](../rep-attestation-frontend/)** | Reputation and reviews interface | ✅ Active |

> See [repositories.md](./repositories.md) for detailed descriptions and relationships.

## 🎯 Use Cases

### For AI Agents
Query OMATrust before API calls to verify legitimacy and compliance, preventing fraud at machine scale.

### For Users  
See verifiable reviews and certifications before downloading apps or trusting websites.

### For Auditors
Publish cryptographic proofs instead of unverifiable PDFs, increasing transparency and trust.

### For Developers
Tokenize once, gain global discoverability. Reputation travels across ecosystems.

### For Enterprises
Cryptographically verify API compliance (GDPR, SOC2, etc.) for automated integrations.

## 🌟 Key Innovations

- **Cross-chain native**: Works on any blockchain, not locked to a single ecosystem
- **DID-based attestations**: Extend trust beyond Web3 to websites and traditional APIs
- **Subsidized interactions**: Users can submit reviews and attestations without gas fees
- **AI-ready verification**: Machine-readable trust signals for automated decision making
- **Credibly neutral governance**: Swiss non-profit ensures no single entity controls the protocol

## 🚦 Getting Started

### For Developers
```bash
# Clone the registry contracts
git clone [registry-contracts-repo]

# Install dependencies
npm install

# Deploy to testnet
npm run deploy:testnet
```

### For Auditors
1. Review the [attestation schemas](../rep-attestation-tools-evm-solidity/schemas-json/)
2. Use the [reputation frontend](../rep-attestation-frontend/) to publish attestations
3. Integrate with existing audit workflows

### For Users
Visit [app.omatrust.org](https://app.omatrust.org) to:
- Search verified applications
- Submit reviews and ratings
- Verify website and API legitimacy

## 📊 Status

**Current Phase**: Pre-launch development
- ✅ Core contracts implemented
- ✅ Specification complete
- ✅ Frontend applications functional
- 🔄 OMAChain deployment in progress
- 📅 Public launch: Q1 2025

## 🤝 Contributing

OMATrust is open source and welcomes contributions:

1. **Join OMA3** - Become a consortium member to participate in governance
2. **Submit Issues** - Report bugs or suggest improvements in relevant repositories  
3. **Contribute Code** - Follow contribution guidelines in each repository
4. **Provide Feedback** - Share thoughts on specifications and protocols

## 📞 Connect

- **Website**: [omatrust.org](https://omatrust.org)
- **OMA3 Consortium**: [oma3.org](https://oma3.org)
- **Documentation**: [docs.omatrust.org](https://docs.omatrust.org)
- **Twitter**: [@OMA3DAO](https://twitter.com/OMA3DAO)

## 📄 License

Documents in this repository are licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
Code repositories have their own licenses - see individual repositories for details.

---

*Building the trust layer of the open internet.*