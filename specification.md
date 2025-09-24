# OMATrust Specification

## Decentralized and Permissionless Trust Layer for the Open Internet

[**Executive Summary	4**](#executive-summary)

[**Introduction	5**](#introduction)

[**Scope	5**](#scope)

[In-Scope	5](#in-scope)

[Out-of-Scope	5](#out-of-scope)

[**References	5**](#references)

[**Definitions	5**](#definitions)

[Abbreviations	5](#abbreviations)

[Definitions	6](#heading=)

[**Specification	6**](#specification)

[OMATrust is comprised of three actors mentioned above:	6](#app-registry-contract)

[● Application Registry Contract	6](#app-registry-contract)

[● Reputation Service	6](#reputation-service)

[App Registry Contract	7](#app-registry-contract)

[JSON Format: versionHistory	8](#json-format:-versionhistory)

[JSON Format: dataUrl	9](#json-format:-dataurl)

[Canonicalization and Hashing	11](#canonicalization-and-hashing)

[● Parsing JSON objects MUST conform to RFC 8259 (ECMA‑404). Inputs that contain comments, single quotes, NaN/Infinity, trailing commas, or other non‑standard extensions MUST be rejected.	11](#parsing-json-objects-must-conform-to-rfc-8259-\(ecma‑404\).-inputs-that-contain-comments,-single-quotes,-nan/infinity,-trailing-commas,-or-other-non‑standard-extensions-must-be-rejected.)

[● To ensure deterministic dataHash values across implementations, Clients MUST fetch dataUrl, canonicalize the returned JSON using JCS (RFC 8785), compute the digest of the the resulting UTF‑8 bytes using the on-chain dataHashAlgorithm, and compare it to the on-chain dataHash value. If they differ, clients MUST treat the manifest as unverified and SHOULD display a warning or hide the app according to UI policy.	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[● Canonicalization (JCS, RFC 8785\) summary:	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[○ Object member names are sorted lexicographically (by Unicode code point).	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[○ Objects are emitted in sorted order; array element order is preserved.	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[○ Numbers are emitted in their minimal form (no leading zeros, no superfluous decimal points or exponents; −0 normalizes to 0).	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[○ Strings are escaped exactly as specified in RFC 8785; no additional Unicode normalization is applied.	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[○ All insignificant whitespace is removed.	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[● Hashing:	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[○ dataHash \= HASH(canonicalUtf8Bytes) where HASH ∈ { sha256, keccak256 }.	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[○ The algorithm used MUST be recorded in dataHashAlgorithm and the digest MUST be encoded as a 0x‑prefixed lowercase hex string.	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[● Guidance:	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[○ Prefer integers and strings over floats where precision matters to avoid cross‑runtime number formatting issues.	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[○ Implementers SHOULD publish golden test vectors for canonicalization+hashing (covering numbers, escape sequences, object key ordering, and nested structures) and verify cross‑runtime determinism to ensure consistency across implementations. Clients are encouraged to use these vectors to validate their consumption of OMATrust data. See Appendix D for example test vectors to guide implementation.	11](#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-\(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures\)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.)

[JSON Format: dataUrl.platforms	11](#json-format:-dataurl.platforms)

[JSON Format: dataUrl.endpoint	12](#json-format:-dataurl.endpoint)

[JSON Format: dataUrl.payments	13](#json-format:-dataurl.payments)

[JSON Format: dataUrl.mcp	13](#json-format:-dataurl.mcp)

[JSON Format: dataUrl.artifacts	15](#json-format:-dataurl.artifacts)

[Field Confirmation	17](#application-ownership-confirmation)

[Control Policy	19](#control-policy)

[Control Policy Justification	20](#control-policy-justification)

[Optional Soulbound Mode	21](#optional-soulbound-mode)

[Indexing Requirements	21](#indexing-requirements)

[App Registry API	22](#app-registry-api)

[Ownership Resolver Contract	25](#ownership-resolver-contract)

[DID ownership is not proactively verified at registration time. Instead, OMA3 uses a dedicated Resolver contract to arbitrate conflicts when multiple parties attempt to claim the same DID.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[Design principles	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[● Keep the App Registry minimal and immutable.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[● Push conflict rules into a pluggable Resolver contract that can be swapped by governance.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[● Allow policies (challenge windows, quorum rules, attester sets) to evolve without redeploying the Registry.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[Process	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[1\. First Claim: The first claimer of a DID mints automatically to streamline onboarding.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[2\. Challenge: A challenger may attempt to rebind a DID by minting the same DID/version combination. The Resolver compares the challenger’s attestation score against the incumbent’s.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[○ Scores are based on the count of valid, non-revoked attestations from an approved list of attesters.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[○ Only attestations older than a global maturation delay (e.g., 72h) count toward scores, creating a rolling challenge window.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[3\. Resolution: If challenger’s matured score strictly exceeds incumbent’s, ownership flips and a new mapping is written to the Deduplicator. Otherwise, the challenge fails and a ConflictAttempted event is emitted.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[4\. Epochs: Each DID has its own counter. Flipping voids prior attestations automatically.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[Attestation model	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[● Attestations are EIP-712 signed by approved attesters (starting with the OMA3 verification server).	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[● Each (attester, did, epoch, claimer) has only one active attestation, which can be updated or revoked.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[● Events record every AttestationUpdated, ConflictAttempted, and OwnershipChanged.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[Notification	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[● Incumbents receive notice via events; off-chain watchers (or EPNS relays) subscribe to these logs.	25](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[● An optional notifier callback can be registered by incumbents for on-chain hooks.	26](#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.)

[This model ensures predictable resolution without upgradeable registries, while allowing flexible evolution of conflict policy.	26](#this-model-ensures-predictable-resolution-without-upgradeable-registries,-while-allowing-flexible-evolution-of-conflict-policy.)

[Reputation Service	26](#reputation-service-1)

[A permissionless app registry without third party attestations leaves users open to fraud.  Adversaries can register malicious, fraudulent, misleading, and counterfeit apps.  To address this problem the Spatial Store leverages the OIF’s robust reputation system.	26](#a-permissionless-app-registry-without-third-party-attestations-leaves-users-open-to-fraud.-adversaries-can-register-malicious,-fraudulent,-misleading,-and-counterfeit-apps.-to-address-this-problem-omatrust-leverages-the-reputation-system.)

[OIF Reputation System	26](#omatrust-reputation-system)

[DID → Index Address Mapping (Normative)	27](#did-→-index-address-mapping-and-searching)

[To enable efficient, per-DID discovery anywhere a Solidity \`address\` is used as an index key (including EAS \`recipient\`), define a deterministic mapping from a DID to an \*\*Index Address\*\*.	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[\*\*MUST:\*\* For any DID \`did\`, compute:	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[indexAddress \= address(uint160(uint256(	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[keccak256("DID:Solidity:Address:v1:" || didHash)	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[)));	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[Where:	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[\- \`didHash \= keccak256(canonicalizeDID(did))\`.	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[\- \`canonicalizeDID(did)\` follows the DID method’s normalization rules (e.g., for \`did:web\`: lowercase host, IDNA/punycode, preserve path, etc.). See the DID hashing appendix / DidLib reference.	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[\*\*Notes & rationale:\*\*	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[\- \*\*Domain separation & versioning:\*\* The ASCII prefix \`DID:Solidity:Address:v1:\` cleanly separates this mapping from any other hash→address scheme and allows future versions (e.g., \`…:v2:\`) without breaking old data.	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[\- \*\*Portability:\*\* Works for EAS \`recipient\`, event partition keys, indexes in other contracts, or any off-chain index keyed by an Ethereum-style address.	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[\- \*\*Semantics:\*\* The Index Address is an \*\*indexing label only\*\*. It does \*\*not\*\* imply control/ownership and MUST NOT be treated as a wallet.	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[\- \*\*Collision risk:\*\* Negligible (≈ 1 / 2¹⁶⁰). The prefix prevents cross-protocol overlap.	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[\*\*Client query pattern (example):\*\*	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[For reviews about \`did\` in EAS, compute \`recipient \= indexAddress\` and filter by \`(schemaUID \= Oma3UserReview@1, recipient)\`. Include \`subjectDidHash\` (the same \`didHash\`) inside the EAS payload so UIs can cross-check \`recipient ↔ subjectDidHash\`.	27](#query-eas-for-attestations-with-recipient-=-indexaddress\(did\)-and-a-given-schema-uid-\(e.g.,-oma3userreview@1\).)

[Linked Identifier Schema	27](#linked-identifier-schema)

[Data URL Schema	29](#data-url-schema)

[Why this is secure	30](#why-this-is-secure)

[Manual review requirements	30](#manual-review-requirements)

[Endorsement Schema	31](#endorsement-schema)

[Certification Schema	31](#certification-schema)

[User Review Schema	31](#user-review-schema)

[App Store Guidance	32](#client-guidance)

[**Change History	33**](#change-history)

[**Appendix A — did:artifact (Provisional)	34**](#appendix-a-—-did:artifact-\(provisional\))

[A.1 Overview	34](#a.1-overview)

[A.2 Identifier Syntax	34](#a.2-identifier-syntax)

[A.3 Computing artifactDid	34](#a.3-computing-artifactdid)

[A.4 Verification (Client Requirements)	35](#a.4-verification-\(client-requirements\))

[A.5 Data Model Integration (normative hooks)	35](#a.5-data-model-integration-\(normative-hooks\))

[A.6 Policy (V1)	36](#a.6-policy-\(v1\))

[A.7 Security Considerations	36](#a.7-security-considerations)

[A.8 DID Index Address Helper	36](#a.8-did-index-address-helper)

[A.9 Forthcoming Method Registration (informative)	37](#a.9-forthcoming-method-registration-\(informative\))

[A.10 Examples (informative)	37](#a.10-examples-\(informative\))

[Editor’s note (remove before publishing)	39](#editor’s-note-\(remove-before-publishing\))

[W.1 Summary (design intent)	40](#w.1-summary-\(design-intent\))

[W.2 Planned artifact shape (non-normative)	40](#w.2-planned-artifact-shape-\(non-normative\))

[W.3 Planned verification algorithm (non-normative)	41](#w.3-planned-verification-algorithm-\(non-normative\))

[W.4 Open items (track in issues)	41](#w.4-open-items-\(track-in-issues\))

# Executive Summary {#executive-summary}

This document is a draft specification proposal for a decentralized and permissionless trust layer for the open internet.  OMATrust leverages the OMA3 Identity Framework (OIF) and has the following components:

1. Registry- a registry of Applications that are identified by OIF DIDs  
2. Attestation Framework- infrastructure for attestations on OIF DIDs, such as cybersecurity certifications.

# Introduction {#introduction}

This specification is a response to the [OMA3 Spatial Store RFP](https://github.com/oma3dao/spatial-store-rfp) and references the [OMA3 Identity Framework specification document](https://docs.google.com/document/d/1tiPgSItVuKEzmzueF0Z6-N3yc3YSGvc1Fyii8nryuHY/edit?tab=t.0).  It also addresses the Metaverse Standards Forum Spatial Store [use case](https://docs.google.com/document/d/1V_6W_rLvk6jV8FaMSzXhQdJwWSoqoE6pAoxlS0HZcJg/edit?tab=t.0), incorporated by reference.

# Scope {#scope}

## In-Scope {#in-scope}

This document aims to solve the following requirements and use cases described in the [Spatial Store RFP](https://docs.google.com/document/d/1NQUEvns5qLmq1w-9lLxOS4Wbr-n1wKupj__aoeikcPQ/).

## Out-of-Scope {#out-of-scope}

Code repositories, tokenomics, business models, and storage implementations are out of scope for this document.

# References {#references}

IWPS Identity Specification Overview  
Spatial Store RFP  
OMATrust Whitepaper  
DID Specification: [https://www.w3.org/TR/did-core](https://www.w3.org/TR/did-core/#terminology)  
DID Spec Registries: [https://www.w3.org/TR/did-spec-registries/](https://www.w3.org/TR/did-spec-registries/)

# Definitions {#definitions}

## Abbreviations {#abbreviations}

In the present document, the following abbreviations apply:

DID	Decentralized Identifier  
DNS	Domain Name System  
HTTP	Hypertext Transfer Protocol  
HTTPS	Hypertext Transfer Protocol Secure  
ID	Identifier  
IdP	Identity Provider  
RP	Relying Party  
TTL	Time To Live  
URL	Uniform Resource Locator  
IWPS	Inter World Portaling System  
VC	(W3C) Verifiable Credential

Relevant External Specification Terminology Sections  
DID Spec	[https://www.w3.org/TR/did-core/\#terminology](https://www.w3.org/TR/did-core/#terminology)  
VC Spec	[https://www.w3.org/TR/vc-data-model-2.0/\#terminology](https://www.w3.org/TR/vc-data-model-2.0/#terminology)

## Definitions

The following definitions are used within the present document. (Mostly reused from IWPS Specification Proposal)

| Term | Definition |
| :---- | :---- |
| Application | A software service that can come in many formats, including a self-contained software application on a Device, an API endpoint, or a smart contract. |
| Client | Software that queries OMATrust to obtain information on an Application. |
| Decentralized Identifier | An identifier that adheres to the W3C DID standard. |
| DID Document | A JSON-LD document containing information about the DID, such as public keys and service endpoints. |
| Owner | The entity that controls the address that owns the app token.  Owner may or may not be the same entity that minted the token. |
| Issuer | An entity that issues credentials as attestations in the Reputation Service. |

In addition to the above definitions all OMA3 specifications use requirements language as described in the [OMA3 working group process](https://github.com/oma3dao/working-group-process/blob/main/working-group-process.md).

# Specification {#specification}

## OMATrust is comprised of three actors mentioned above: {#app-registry-contract}

##  {#app-registry-contract}

* ## Application Registry Contract {#app-registry-contract}

* Ownership Resolver Contract

* ## Reputation Service {#reputation-service}

The following applies to the whole document:

* “string” means a UTF-8 string  
* “URL” means a string in a specific format  
* “JSON” means a string in JSON format  
* \[\] is meant to signify an array of whatever is inside the brackets.

## App Registry Contract {#app-registry-contract}

The Application Registry contract tokenizes applications on the blockchain as an NFT.  This document describes the specifications for this contract in a platform-agnostic manner.  

Every app registry NFT stores the following information associated with an application.

| Field | Format | Description | Ownership Proof | Req? | Mutable? |
| ----- | ----- | ----- | ----- | ----- | ----- |
| did | string | See Table 3 | Y | Y | N |
| fungibleTokenId | string | CAIP-19 token ID | Only if address that minted the ERC-20 contract is different  | N | N |
| contractId | string | CAIP-10 “gameplay” contract address | Only if address that minted the contract is different  | N | N |
| versionHistory | \[object\] | Array of released version structs, append only | None | Y | Y |
| status | enum | Active, deprecated, replaced | None | Y | Y |
| minter | address | Address of the transaction signer. | Y (built into ERC-721) | Y | N |
| owner | address | Current owner (built into ERC-721) | N | Y | Y (soulbound optional) |
| dataUrl | URL | URL to offchain data | Y | Y | Y |
| dataHash | string | Hash of the JSON returned by dataUrl | None | Y | Y |
| dataHashAlgorithm | string | The hash algorithm used to compute dataHash. Values: "keccak256", "sha256" | None | Y | Y |
| traitHasheskeywordHashes | \[string\] | A structure of hashed traitskeywords.  Implementation is different for each VM. ​​Implementations SHOULD cap on-chain traitHasheskeywordHashes to ≤ 20 entries to mirror the off-chain keywords cap, and clients MUST NOT assume more than 20 are indexed.  See Appendix C. | None | N | Y |
| interfaces | \[enum\] | An unordered set of interface capability codes. Multiple capabilities may be present.  Example: bitmap with 0 \= human2 \= api4 \= mcp 8 \= smart contract | None | Y | N |

Table 1: Application Registry Onchain DataMetadata.

### JSON Format: *versionHistory* {#json-format:-versionhistory}

The objects in the *versionHistory* array have the following fields:

| Value | Format | Required |
| ----- | ----- | ----- |
| major | Int | Y |
| minor | Int | Y |
| patch | Int | Y |

### JSON Format: *dataUrl* {#json-format:-dataurl}

*dataUrl* points to an endpoint that returns a JSON object with offchain data.  The JSON object has the following top level fields depending on the value of the *type* field in the NFT contract:

| Value | Format | Description | Interface |  |  |
| ----- | ----- | ----- | ----- | ----- | ----- |
|  |  |  | **0** | **2** | **4** |
| name | string | App name. Matches ERC-721 metadata extension. | Y | Y | Y |
| external\_url | URL | URL of the app’s market website. Matches ERC-721 metadata extension. | O | O | O |
| image | URL | URL to the application icon.  Matches ERC-721 metadata extension. | Y | O | O |
| description | string | Long description of the application.  Max 4000 chars. Matches ERC-721 metadata extension. | Y | Y | Y |
| publisher | string | Publisher name. | Y | Y | Y |
| summary | string | Short description of the application.  Max 80 chars. | Y | O | O |
| owner | string | CAIP-10 address of the owner of the app token NFT.  Used to confirm ownership of the dataUrl JSON (see “Field Confirmation” below). | Y | Y | Y |
| screenshotUrls | \[URL\] | JSON urls field contains an array of URLs to screenshot images. | Y | N | N |
| videoUrls | \[URL\] | JSON urls field contains an array of URLs to videos. | O | N | N |
| 3dAssetUrls | \[URL\] | JSON urls field contains an array of URLs to 3D assets (GLB, USDZ, etc.). | O | N | N |
| legalUrl | URL | URL to legal agreements like license, terms of service, privacy policy, etc. | O | O | O |
| supportUrl | URL | URL to get support. | O | O | O |
| iwpsPortalUrl | URL | See [IWPS Spec](https://github.com/oma3dao/iwps-specification). | O | O | N |
| traitskeywords | \[string\] | An array of max 20 traitskeyword strings and the total char count cannot exceed 120\. See Appendix C. | O | O | O |
| interfaceVersions | \[string\] | Array of supported versions of the interface | N | O | O |
| platforms | JSON | Object of platforms supported by the app. | Y | N | N |
| endpoint | JSON | Details on the API endpoint (see below) | N | Y | O |
| payments | \[JSON\] | Array of payment mechanisms (see below).  If this exists then payment is required to use the service. | O | O | N |
| artifacts | JSON | Allows clients to verify content (e.g.- binaries) | O | O | N |
| mcp | JSON | Implements the MCP server specification (see below) | N | O | N |
| a2a | URL | URL to the \~/well-known/agent-card.json endpoint as defined in the A2A standard | N | O | N |

Table 2: Application offchain data.

### Canonicalization and Hashing {#canonicalization-and-hashing}

* ### Parsing JSON objects MUST conform to RFC 8259 (ECMA‑404). Inputs that contain comments, single quotes, NaN/Infinity, trailing commas, or other non‑standard extensions MUST be rejected. {#parsing-json-objects-must-conform-to-rfc-8259-(ecma‑404).-inputs-that-contain-comments,-single-quotes,-nan/infinity,-trailing-commas,-or-other-non‑standard-extensions-must-be-rejected.}

* ### To ensure deterministic dataHash values across implementations, Clients MUST fetch dataUrl, canonicalize the returned JSON using JCS (RFC 8785), compute the digest of the the resulting UTF‑8 bytes using the on-chain dataHashAlgorithm, and compare it to the on-chain dataHash value. If they differ, clients MUST treat the manifest as unverified and SHOULD display a warning or hide the app according to UI policy. {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

* ### Canonicalization (JCS, RFC 8785\) summary: {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

  * ### Object member names are sorted lexicographically (by Unicode code point). {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

  * ### Objects are emitted in sorted order; array element order is preserved. {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

  * ### Numbers are emitted in their minimal form (no leading zeros, no superfluous decimal points or exponents; −0 normalizes to 0). {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

  * ### Strings are escaped exactly as specified in RFC 8785; no additional Unicode normalization is applied. {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

  * ### All insignificant whitespace is removed. {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

* ### Hashing: {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

  * ### dataHash \= HASH(canonicalUtf8Bytes) where HASH ∈ { sha256, keccak256 }. {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

  * ### The algorithm used MUST be recorded in dataHashAlgorithm and the digest MUST be encoded as a 0x‑prefixed lowercase hex string. {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

* ### Guidance: {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

  * ### Prefer integers and strings over floats where precision matters to avoid cross‑runtime number formatting issues. {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

  * ### Implementers SHOULD publish golden test vectors for canonicalization+hashing (covering numbers, escape sequences, object key ordering, and nested structures) and verify cross‑runtime determinism to ensure consistency across implementations. Clients are encouraged to use these vectors to validate their consumption of OMATrust data. See Appendix D for example test vectors to guide implementation. {#implementers-should-publish-golden-test-vectors-for-canonicalization+hashing-(covering-numbers,-escape-sequences,-object-key-ordering,-and-nested-structures)-and-verify-cross‑runtime-determinism-to-ensure-consistency-across-implementations.-clients-are-encouraged-to-use-these-vectors-to-validate-their-consumption-of-omatrust-data.-see-appendix-d-for-example-test-vectors-to-guide-implementation.}

Note: Contracts do not validate JCS; they only store the algorithm \+ digest. JCS compliance and hash correctness are enforced off-chain by clients and indexers.

### JSON Format: *dataUrl.platforms* {#json-format:-dataurl.platforms}

If *interfaces* \= 0, The *platforms* JSON object MUST contain one or more of the following fields depending on how the human user interacts with the app:

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| web | JSON | Web is supported | O |
| ios | JSON | iOS is supported | O |
| android | JSON | Android is supported | O |
| windows | JSON | Windows is supported | O |
| macos | JSON | MacOS is supported | O |
| meta | JSON | Meta Quest is supported | O |
| playstation | JSON | Playstation is supported | O |
| xbox | JSON | XBox is supported | O |
| nintendo | JSON | Nintendo is supported | O |

A platform field is a JSON object that has the following fields:

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| launchUrl | string | URL to launch the app | Y |
| supported | \[string\] | iPhone, iPad, x64, arm64, etc. | O |
| downloadUrl | string | URL to download a binary | O |
| artifactDid | string | See Appendix A | O |

### JSON Format: *dataUrl.endpoint* {#json-format:-dataurl.endpoint}

The *endpoint* JSON object contains the following fields:

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| url | string | URL of the endpoint | Y |
| format | string | See Appendix C | O |
| schemaUrl | string | URL to API documentation | O |

For *interface* \= 4 (contracts), the chain ID is taken from the DID (did:pkh with CAIP-10 ID). Clients can then determine the format the RPC endpoint requires based on the chain ID. 

### JSON Format: *dataUrl.payments* {#json-format:-dataurl.payments}

The *payment* array contains at least one JSON object if payment is required.  The possible JSON objects are differentiated by the *type* field.  There are currently only two possible values for the *type* field: *x402* and *manual*.  The below tables describe these two objects.

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| type | string | “x402” | Y |
| url | string | Base URL of the x402 endpoint. Clients SHOULD call GET {url}/supported for live tuples (source of truth). If this field is not present, use *dataUrl.endpoint*. | O |
| chains | \[string\] | CAIP‑2 chain IDs this service supports. | O |

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| type | string | “manual” | Y |
| url | string | Describes pricing information and payment mechanics. | O |

### JSON Format: *dataUrl.mcp* {#json-format:-dataurl.mcp}

This object represents the MCP specification and gives agents the information they need to interface with an MCP server.  The reader is referred to the specification of MCP v1.0 (modelcontextprotocol.io, Section 3, Server Metadata), which has descriptions of each field and is incorporated by reference.  

The following are the JSON fields for the *mcp* field:

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| tools | \[JSON\] | See below | Y |
| resources | \[JSON\] | See below | Y |
| prompts | \[JSON\] | See below | Y |
| transport | JSON | See below | Y |
| authentication | JSON | See below | Y |

The *tools* array contains JSON objects with the following fields:

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| name | string |  | Y |
| description | string |  | Y |
| inputSchema | JSON |  | Y |
| annotations | JSON |  | N |

The *resources* array contains JSON objects with the following fields:

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| uri | string |  | Y |
| name | string |  | Y |
| description | string |  | N |
| mimeType | string |  | N |

The *prompts* array contains JSON objects with the following fields:

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| name | string |  | Y |
| description | string |  | Y |
| arguments | \[JSON\] |  | N |

The *transport* JSON object contains the following fields:

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| http | JSON |  | N |
| stdio | JSON |  | N |

The *authentication* JSON object contains the following fields:

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| oauth2 | JSON |  | N |
| blockchain | JSON |  | N |

### JSON Format: *dataUrl.artifacts* {#json-format:-dataurl.artifacts}

*dataUrl.artifacts* is a JSON map keyed by the value of *artifactDid* (see Appendix A). It carries integrity and distribution details for any verifiable payload referenced from *dataUrl.platforms*.

Key format: **`did:artifact:<cidv1>`** (CIDv1 base32 of the artifact bytes)

The most prevalent use is for downloadable binaries as it allows clients to check the legitimacy of a binary before downloading it. This object reflects supply‑chain lessons baked into Apple notarisation, Windows Authenticode, Sigstore, and SLSA.

The following table shows the common fields in each object in the mapping.

| Value | Format | Description | Required |
| ----- | ----- | ----- | ----- |
| type | string | "binary", "container", or "website" | Y |
| sriManifest | JSON | See Appendix B | N |
| os | string | “windows”, “macos”, “linux” | Y (except website) |
| architecture | string | “x64”, “arm64” | Y (except website) |
| mimeType | string |  | N |
| downloadUrls | \[string\] | Array of download URLs | N |
| sizeBytes | Integer |  | N |
| provenanceUrl | URL | SLSA provenance JSON | N |
| signatureUrls | \[JSON\] | Example:  \[{“sigUrl”:”blah”, “sigAlgorithm”:”pgp/x509”}\] | N |
| otherHashes | \[JSON\] | Example: \[{“hash”:”blah”,”algo”:”sha256”}\] | N |
| notarization | TBD |  | N |
| transparencyLog | JSON | See below | N |

JSON example for the *binaries* field:

```json
[{
  "url": "https://dl.oma3.dev/pkg/app-v2-macos.dmg",
  "os": "windows",
  "architecture": "x64",
  "hash": "sha256:4a0d479bd5db...9e",
  "hashes": [
    {
      "algo": "blake3",
      "digest": "e05ac4bb..."
    },
    {
      "algo": "sha512",
      "digest": "8b7e1a..."
    }
  ],
  "sig": {
    "url": "https://dl.oma3.dev/pkg/app-v2-macos.dmg.sig",
    "alg": "x509"
  },
  "notarization": {
    "ticket": "MIIb8TCCAZmgAwIBAgIQA..."
  },
  "transparencyLog": {
    "type": "rekor",
    "entryId": "sha256:bf33..."
  },
  "provenance": "https://dl.oma3.dev/pkg/app-v2-slsa.json",
  "sizeBytes": 1582034
}]
```

### Application Ownership Confirmation {#application-ownership-confirmation}

To increase trust in the onchain and offchain data described in an app token, the app owner SHOULD provide proof of ownership of the data.  Clients of the registry SHOULD confirm ownership of certain data provided by the app owner.  This section describes the various mechanisms used for each field.

did:web\- if the format of *did* is did:web, the owner MUST support the [did:web Method Specification](https://w3c-ccg.github.io/did-method-web/#well-known-did).  This means the owner needs control over the url endpoint to provide the *did.json* file.  An example DID Document JSON file:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/secp256k1recovery-2020/v2"
  ],
  "id": "did:web:example.com",
  "verificationMethod": [
    {
      "id": "did:web:example.com#app-owner-key",
      "type": "EcdsaSecp256k1RecoveryMethod2020",
      "controller": "did:web:example.com",
      "blockchainAccountId": "eip155:1:0x89a932207c485f85226d86f7cd486a89a24fcc12"
    }
  ],
  "authentication": [
    "did:web:example.com#app-owner-key"
  ]
}
```

Before tokenizing an application with a *did* of did:web, the owner MUST get an attestation from an approved issuer that checks that the JSON object returned by \[url\]/.well-known/did.json contains the address that will mint the token.  The issuer MUST retrieve the DID Document located at \[url\]/.well-known/did.json and verify that the owner address appears as an array element of the verificationMethod field in the DID Document returned by the endpoint.  The app registry minting function requires this attestation before allowing the mint to continue.

The client reading the *did* field SHOULD independently confirm that the owner of the NFT matches the address contained in *blockchainAccountId*.

did:pkh: This DID method is for tokenizing a smart contract application. Smart contracts do not need to be tokenized in order for users to file attestations on them. Attestations can be filed directly with an attestation service using the DID → Index Address Mapping method (see below). Tokenizing a smart contract is primarily for discovery and usage information.

If a smart contract is tokenized, the issuer MUST confirm that the address minting the smart contract token is controlled by the same entity that either administered or deployed the contract:

* If the contract has an admin, the admin control address MUST be used.  
* If the contract is immutable, the deploying address MUST be used.

 Verification can be done as follows:

1. Determine the controlling address of the contract  
   * Upgradeable contracts:  
     * Inspect the contract’s metadata or storage to locate the address that holds upgrade authority.  
     * If an intermediate controller contract exists (e.g., an admin module), resolve the controlling address from that contract.  
   * Contracts with explicit owner/admin roles:  
     * Query the contract for the address associated with its owner or administrative role.  
   * Immutable contracts:  
     * If no admin or owner role is detectable, the deploying account from the creation transaction is the controlling address.  
       (Example: On Ethereum, this may be read from well-known storage slots such as `eip1967.proxy.admin`, or via calls to functions like `owner()` or role checks in `AccessControl`.)  
2. Compare with the minter of the registry token  
   * The address that minted the registry token MUST equal the controlling address derived above.  
   * If they match, ownership is verified.  
3. Fallback: attestation path  
   * If the registry token is to be minted by a different address (e.g., a multisig, DAO, or delegated key) than the controlling address, the issuer MUST make an attestation binding the minting address to the controlling address.  
   * Clients SHOULD accept such an attestation only if it is issued by a trusted issuer.  
4. Failure to verify  
   * If no match is found and no valid attestation is present, clients MUST treat the tokenized contract as unverified.

Wallet-based DIDs:  If *did* uses a DID method that leverages public keys (e.g.- a wallet address), the client should check that either:

1. The DID is derived from the same key as the owner address that minted the NFT, or  
2. There is an attestation (see did:pkh above) that proves the owner controls both the DID key and the minting key.

Other DIDs:  Other did method verification where the did value doesn’t match the owner:  TBD

### DataURL Confirmation

The JSON returned by the *dataUrl* API endpoint MUST contain the *owner* field, and its value MUST match the NFT owner address.  Furthermore, the owner SHOULD ensure the existence of a trusted third-party attestation that verifies certain dataUrl fields such as:

* name  
* publisher  
* summary  
* description  
* image  
* screenshotUrls  
* videoUrls  
* 3dAssetUrls  
* external\_url

### Other URL Confirmation

* URLs that use a domain that has already been verified by the above did:web method do not need to be re-verified.    
    
* Clients SHOULD place higher trust in content addressable URLs such as IPFS or Filecoin URLs, as the content in these URLs cannot be changed without changing the URL.  
    
* URLs that return a JSON object (e.g.- screenshotUrls) can be verified by accessing the verificationMethod field of the JSON object.  The format of the verificationMethod field is the same as the verificationMethod field of a DID Document JSON strong (see above) and clients verify the owner in the same manner.  
    
* All other URLs SHOULD be verified using the same mechanism to verify did:web DIDs or by checking a Linked Identifier attestation.   
    
* If the URL returns a media file, the media file MAY have the owner address embedded in the file in some manner.  
    
* URLs that cannot be verified SHOULD be treated with caution. 

### Control Policy {#control-policy}

App Registry contracts MUST manage when version updates are required and when new NFT mints are required.  These policies are based on policies of existing app stores that have years of experience mitigating fraudulent behavior.  

* Each DID requires a different token in the Registry.  It is the equivalent of Apple’s Bundle ID.    
* Different major versions of a specific DID also require a new token.

The following table details versioning rules for certain onchain fields.

| Field | Change Rule | Listing Effect |
| ----- | ----- | ----- |
| `fungibleTokenId` | MUST mint a new DID on change | Always a new listing. fungibleTokenId MUST NOT change across majors of the same DID; changing it requires a new DID. |
| `contractId` | MUST mint with same DID and major++ | Breaking changes trigger major version bump; preserves CAIP‑19 uniqueness. |
| `owner` | MUST use same DID and major++ | Governance changes audited per major; no silent owner transfer. |
| `interfaces` | MUST Minor++  | Backward‑compatible API additions gated by minor bump |
| `dataHash` | MUST Patch++  | Bug fixes and metadata tweaks gated by patch bump. |

An App Registry contract MUST enforce the following rules:

| Change Type | On‑chain Requirement | Policy Quorum | Store UI Behavior |
| ----- | ----- | ----- | ----- |
| **Major version (breaking)** | Mint new `(did, major++)` NFT | ≥ 2 EIP‑712 attesters | Old major remains in “previous versions” until new attestations complete. |
| **Minor update (additive)** | Update `interfaces` on existing NFT | ≥ 1 EIP‑712 attester | Visible immediately with “pending review” badge. |
| **Patch update (fixes)** | Update `dataHash` on existing NFT | ≥ 1 EIP‑712 attester | Seamless update; no visible version change. |

Table 3:  Allowed DID methods for Application Registry DID field.

### Control Policy Justification {#control-policy-justification}

* Apple’s App Store ties immutability to the Bundle ID, forbidding any Bundle ID change once an app is live, so users always know they’re running the same canonical app. Google Play enforces the same rule on the Android Package Name, requiring publishers to create an entirely new listing if they change it.   
* Swapping the fungibleTokenId on-chain is equivalent to issuing an infinite‑mint ERC‑20 rug pull, so we freeze that field and demand a new DID/NFT to alter it.    
* Because CAIP‑19 encodes the contract address in the asset ID, any breaking executable change must mint a new NFT keyed by (did,major) to maintain deterministic asset lookups.   
* Non‑breaking API additions only require a minor bump in version number, signaling backward compatibility, while metadata or binary tweaks controlled by dataHash require only a patch bump. 

### Optional Soulbound Mode {#optional-soulbound-mode}

When enabled, transfers and approvals MUST be rejected; minting and burning remain allowed.

API impact: No new fields are required (mode can be implicit or a boolean per token), but behavior MUST be observable via a read method, e.g., isSoulbound(tokenId).

UI guidance: Stores SHOULD visually label soulbound apps.

### Indexing Requirements {#indexing-requirements}

Implementations MUST make all state changes observable for indexers in a machine-consumable way.

* Path A — Log/Event stream (if the platform has it):  Provide structured event/log records for: mint, status change, version bump, data hash change, capability change, keyword change. (Name and exact fields are platform-profile specific.)

* Path B — Deterministic state deltas (if no logs):  Expose monotonic counters that indexers can poll:  
  * A global changeSeq that increments on any change, plus a getChangesSince(seq) or paginated listChanges(startSeq, limit) returning typed change records; or  
  * Per-token lastChangeSeq plus a global change journal accessible via RPC/query.

* Profiles: EVM profile SHOULD use events; other profiles MAY prefer counters/journals. Either path MUST provide enough data for a stateless indexer to reach eventual consistency.

### App Registry API {#app-registry-api}

The Application Registry API allows developers to access the Application Registry.  The API provides native blockchain JSON-RPC smart contract APIs (recommended) and a web2 API.  The OMATrust Registry contract supports the following functions:

* mint: mints an Application.  
* updateStatus:  updates the status of an Application.  
* getAppsByStatus:  gets all the Applications (with pagination) of a certain status.  
* getApps:  gets all Applications with ACTIVE status.  
* getAppDIDsByStatus:  returns a list of Application DIDs of a certain status.  
* getAppDIDs: returns a list of Application DIDs with ACTIVE status.  
* getAppsByMinter:  gets all the Applications minted by an address.  
* getApp: returns an Application object given a DID.  
* getDIDDocument:  returns an Application is DID Document format given a DID.

The Application Registry supports the following DID methods. All other DIDs will fail.

| DID Method | DNS TLD |
| ----- | ----- |
| did:web3 | any? |
| did:pkh | realm |
| did:artifact | world |
| did:a2a | app |

Table 4:  Allowed DID methods for Application Registry DID field.

To put data on the Application Registry a developer must send a signed transaction to the Application Registry’s mint smart contract function through a native RFC endpoint of the contract (most often through an [OMA3 front end website](https://appregistry.oma3.org)).  The transaction MUST include the required parameters in Table 1\.  The transaction MAY include one or more of the optional parameters in Table 1\.

The Application Registry DID standard API returns a DID Document using a DID Resolver.  Here’s an example of a DID Resolver request to a REST API using curl asking for a DID Document given an Application DID:

```shell
curl -X GET https://appresolver.oma3.org/1.0/identifiers/did:web:upland.me
```

The request returns the following DID Document:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1"
  ],
  "id": "did:web:upland.me",
  "verificationMethod": [
    {
      "id": "did:web:upland.me",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "controller": "did:web:123456789abcdef",
      "publicKeyBase58": "04a34b8b56cf4d... "
    }
  ],
  "authentication": [
    "did:ethr:0x123456789abcdef#key-2"
  ],
  "service": [
    {
      "id": "did:example:123456789abcdef#metadata",
      "type": "MetadataService",
      "serviceEndpoint": {
        "name": "Upland",
        "version": 1.0,
        "icon-url": "https://upland.me/icon",
        "status": "active",
        "iwps-portal-url": "https://upland.me/iwps-portal-api",
        "description": "A web3 property game",
        "screenshots": [
          "https://example.com/screenshot1.png",
          "https://example.com/screenshot2.png"
        ],
        "owner-address": "did:ons:upland.realm",
        "application-token": "8g82898g98234-ba..."
      }
    }
  ]
}
```

Here’s an example of the same resolver request to an EVM JSON-RPC node at localhost 127.0.0.1 assuming the Application Resolver smart contract is located at address “0x9fE46736679d2D9a65F0992F2272dE9f3c7fa6e0” using curl:

```shell
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_call","params":[0:{"to":"0x9fE46736679d2D9a65F0992F2272dE9f3c7fa6e0","value":"did:web:upland.me"},1:"latest"]}' 127.0.0.1:8545
```

The request returns the same DID Document as the REST API call.  

Reputation Integration:  TBD

## Ownership Resolver Contract {#ownership-resolver-contract}

### DID ownership is verified at token minting time. OMA3 uses a dedicated **Resolver contract** to confirm ownership and arbitrate conflicts when multiple parties claim to own the same DID. {#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.}

### **Process** {#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.}

1. ### **First Attestation:** The owner of a DID gets an attestation from an approved issuer to that confirms ownership of the DID.  This confirmation process can be manual (e.g.- in conjunction with a cybersecurity audit) or automatic (e.g.- a server that checks .well-known/did.json programmatically).  The Resolver contract holds the attestation onchain. {#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.}

2. ### **First Mint:** With the ownership attestation in place, the owner mints the application with the wallet address in the attestation.  The Resolver contract checks the attestation before confirming the mint.

3. ### **Challenge:** A challenger may attempt to rebind a DID by minting the same DID/version combination.  {#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.}

   * ### The challenger MUST either ask the original issuer to reverse the attestation (issuing a new attestation) or enlist at least two other approved issuers to attest to the challenger’s ownership of the DID in question.  The Resolver compares the challenger’s attestation score against the incumbent’s.

   * ### Scores are based on the count of valid, non-revoked attestations from an approved list of issuers.

   * ### Only attestations older than a global **maturation delay** (e.g., 72h) count toward scores, creating a rolling challenge window.

4. ### **Resolution:** If the number of challenger attestations strictly exceed incumbent’s, ownership flips. Otherwise, the challenge fails. {#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.}

### **Attestation model** {#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.}

* ### Attestations are EIP-712 signed by approved attesters (starting with the OMA3 verification server). {#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.}

* Each *(attester, did, epoch, claimer)* has only one active attestation, which can be updated or revoked.  
* Events record every *AttestationUpdated, ConflictAttempted*, and *OwnershipChanged*.

### **Notification** {#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.}

* ### Incumbents receive notice via events; off-chain watchers (or push notification services) subscribe to these logs. {#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.}

* ### An optional notifier callback can be registered by incumbents for on-chain hooks. {#an-optional-notifier-callback-can-be-registered-by-incumbents-for-on-chain-hooks.}

### This model ensures predictable resolution without upgradeable registries, while allowing flexible evolution of conflict policy. {#this-model-ensures-predictable-resolution-without-upgradeable-registries,-while-allowing-flexible-evolution-of-conflict-policy.}

## Reputation Service {#reputation-service-1}

### A permissionless app registry without third party attestations leaves users open to fraud.  Adversaries can register malicious, fraudulent, misleading, and counterfeit apps.  To address this problem OMATrust leverages the Reputation System. {#a-permissionless-app-registry-without-third-party-attestations-leaves-users-open-to-fraud.-adversaries-can-register-malicious,-fraudulent,-misleading,-and-counterfeit-apps.-to-address-this-problem-omatrust-leverages-the-reputation-system.}

### OMATrust Reputation System {#omatrust-reputation-system}

The OMATrust Reputation System leverages and augments existing services like Ethereum Attestation Service.  It is comprised of the following components:

1. Attestation Schemas:  OMA3 defines several schemas for different attestations, from user reviews to cybersecurity certifications.  These are listed below.  
     
2. Cross Chain Addresses:  Instead of using chain-specific blockchain addresses to identify the attestation subject, OMATrust uses hashed DIDs in the same format as addresses (see DID → Index Address Mapping below), which support web domains as well as blockchain addresses.  
     
3. Resolver:  the Resolver contract stores onchain attestations related to ownership, as described above.

### DID → Index Address Mapping and Searching {#did-→-index-address-mapping-and-searching}

To enable efficient per-DID discovery in contexts where a Solidity *address* is used as an index key (including the *recipient* field in EAS), OMATrust defines a deterministic mapping from a DID to an Index Address.

#### **Computing the Index Address**

For any *did*, the corresponding Index Address is derived as follows:

```
library DidIndex {
    /// @notice Compute the DID Index Address used for EAS recipient or other address-keyed indexes.
    /// @dev didHash = keccak256(canonicalizeDID(did))
    function toAddress(bytes32 didHash) internal pure returns (address) {
        // Domain-separated, versioned prefix for portability and clarity.
        bytes32 h = keccak256(abi.encodePacked("DID:Solidity:Address:v1:", didHash));
        return address(uint160(uint256(h)));
    }
}
```

### Where: {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

* ### *`canonicalizeDID(did)`* applies normalization rules defined by the DID method. For example: {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

  * ### **did:web**: lowercase the host, apply IDNA/punycode for international domains, preserve the path. {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

  * ### **did:pkh**: use the canonical chain/account encoding. {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

* ### *`didHash`* is the keccak256 digest of the canonicalized DID string. {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

### **Design Rationale:** {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

* ### **Domain separation & versioning:** The ASCII prefix *`DID:Solidity:Address:v1:`* ensures compatibility and clear separation from other schemes. Future revisions can use updated prefixes (e.g., `…:v2:`). {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

* ### **Portability:** The Index Address can be used anywhere an *`address`* index is expected—EAS recipients, event partition keys, or contract mappings. {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

* ### **Semantics:** The Index Address is a **label for indexing only**. It is not a wallet and does not imply control or ownership. {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

* ### **Collision risk:** Negligible (≈ 1 / 2¹⁶⁰). The prefix further reduces overlap with unrelated mappings.

### Attestation Querying

### Clients can retrieve attestations related to a DID by computing its Index Address and filtering attestations accordingly. {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

### **Example query flow:** {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

1. ### Compute *`indexAddress(did)`* using the algorithm above. {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

2. ### Query EAS for attestations with *`recipient = indexAddress(did)`* and a given schema UID (e.g., *`Oma3UserReview@1`*). {#query-eas-for-attestations-with-recipient-=-indexaddress(did)-and-a-given-schema-uid-(e.g.,-oma3userreview@1).}

3. ### Within each attestation payload, confirm that *`subjectDidHash`* matches the DID hash used to derive the recipient. This prevents mismatches or spoofing.

### EAS Integration: Recipient Rule

### When storing an attestation about a DID in EAS:

* ### The *`recipient`* field MUST equal the computed *`indexAddress(did)`.*

* ### The attestation payload MUST include *`subjectDidHash`*, which is exactly the *`didHash`* derived during Index Address computation.

#### **Example Schema: *`Oma3UserReview@1`***

### A review attestation payload might be structured as:

```
struct UserReviewPayload {
    uint8 rating; // Rating: 0–100 (basis points) or scaled 0–5 × 20
    bytes32 contentHash; // Hash or CID digest of the review content
    bytes5 locale; // Optional: BCP-47 locale code (e.g., "en-US")
    uint16 version; // Optional: schema/content version indicator
    bytes32 subjectDidHash; // MUST equal the didHash used in recip...
}
```

#### **Discovery Pattern**

### To fetch reviews about a DID:

```
address recipient = indexAddress(did);
// Query EAS for attestations where:
// schemaUID == Oma3UserReview@1
// recipient == indexAddress(did)
```

### Then decode the attestation payloads and resolve *`contentHash`* for off-chain content when needed.

### Clients can retrieve attestations related to a DID by computing its Index Address and filtering attestations accordingly.

### Linked Identifier Schema {#linked-identifier-schema}

This schema provides additional attestations on control of an ID by another ID.  For example, attesting that the entity that controls one blockchain address also controls a different blockchain address.  Examples of IDs that can be entered into a Linked Identifier attestation:

* DID:  DIDs need an attestation signed by the wallet that is associated with the DID.  
* Blockchain address:  Similar to DIDs, blockchain addresses need an attestation signed by the blockchain address.  
* Web3 names:  web3 domains need an attestation that the developer owns the wallet address that controls the web3 domain.  
* URL:  URLs need an attestation of a third party that uses a mechanism to prove the developer controls the domain.  

In some cases the Issuer of an attestation can be automated.  It is easy for software to get signatures from two different blockchain accounts to prove common control.  Others, such as offchain IDs like passport numbers, may require manual confirmation.  

Below are some Linked Identifier schema field values and verification processes that Issuers could implement before issuing a Linked Identifier attestation.

| Field | Value | Description |
| ----- | ----- | ----- |
| Issuer ID | Wallet address | ID of the attester/issuer |
| Holder ID | DID, wallet address, or Web3 domain | ID of the Application |
| Linked ID | Wallet address | Wallet address of Application owner |
| AttestationDate | block.timestamp |  |
| Method | enum | Method used to verify the metadata |

For verifying that a Holder ID wallet address also controls a URL domain, a third party Issuer could follow this procedure, which is similar to proving domain ownership for a website SSL certificate:

1. Developer goes to the Issuer’s website and enters the domain they are trying to verify.  
2. Developer uses the website to sign a transaction with the same wallet they used with the Application Registry.    
3. Issuer website gives developer a random value.  
4. Developer stores the value in the domain’s DNS TXT Record.  
5. Developer tells the Issuer website to check the DNS records.  
6. If the Issuer finds records contain the value, the Issuer sends a signed transaction attestation to the blockchain with the above format.

To bind a \``` did:web` `` identifier to a blockchain wallet address, the schema requires an attestation that proves control of both:

* **Holder ID:** `did:web:example.com`  
* **Linked ID:** `eip155:1:0xabc...`

**Verification flow:**

1. The developer serves a DID document at `https://example.com/.well-known/did.json`containing a `verificationMethod` entry with `blockchainAccountId` equal to the wallet address.  
2. The developer signs a challenge message with that wallet.  
3. The Issuer (e.g., OMA3 verification server) validates both:  
   * the DID document includes the wallet address, and  
   * the wallet produced a valid signature.  
4. The Issuer then issues a Linked Identifier attestation with fields:  
   * `Issuer ID`: attester wallet  
   * `Holder ID`: `did:web:example.com`  
   * `Linked ID`: `eip155:1:0xabc...`  
   * `AttestationDate`: block.timestamp  
   * `Method`: `didWebToWalletSignature`

**Revocation/updates:** A new attestation overwrites the old one, or the Issuer can revoke it explicitly.

**Usage:** Clients and registries SHOULD only consider a did:web binding valid if a live Linked Identifier attestation exists proving control of both identifiers.

### Data URL Schema {#data-url-schema}

Every app token stores two on-chain pointers to its mutable metadata:

| Slot | Purpose |
| ----- | ----- |
| `dataUrl` (`string`) | HTTP / IPFS endpoint that returns the JSON manifest for this token. |
| `dataHash` (`bytes32`) | Hash of the JCS‑canonicalized bytes of the JSON served at dataUrl. |

To prevent silent or misleading edits, each manifest must be covered by a third-party attestation whose schema includes the *same* `dataHash`. The three artefacts work together as follows:

| Step | Actor | Action |
| ----- | ----- | ----- |
| 1 .  Publish manifest | App developer | Upload the JSON to `dataUrl`; compute `dataHash = hash(bytes(JSON))`. |
| 2 . Request attestation | Human reviewer (attester) | Manually inspect the JSON (name, publisher, screenshots, etc.). • If compliant, issue an EAS-compatible attestation:   `{ tokenId, dataHash, schema: NAME_COMPLIANCE, issuedAt }` |
| 3 .  Register hash | App developer | Call `updateMetadata(tokenId, dataUrl, dataHash)`; the contract rejects the call unless a matching attestation exists. |
| 4 . Client verification | Wallet / marketplace | a) Fetch `dataUrl` JSON. b) Compute `hash(JSON)` and compare to on-chain `dataHash`. c) Confirm an attestation referencing that same hash is still valid. d) Only display the app if both checks pass. |

#### Why this is secure {#why-this-is-secure}

* Immutability guarantee – Any byte-level change (even a new screenshot) alters the hash; the old attestation no longer matches, so UIs hide the app until a new review is issued.

* Reviewer accountability – The attestation is signed by an address in the front-end’s trusted-attester list; multiple independent attesters can coexist, avoiding central gate-keeping.

* Audit trail – Every approved version leaves an on-chain record (`dataHash` \+ attestationId), giving provable history for compliance audits.

#### Manual review requirements {#manual-review-requirements}

A reviewer MUST verify at minimum:

1. Brand & trademark – No infringing or misleading app/publisher names.

2. Visual assets – Screenshots, icons, videos accurately represent the product.

3. Policy compliance – No illicit content, malware links, or disallowed keywords.

Once satisfied, the reviewer signs the attestation that binds *their* approval to the specific `dataHash`. Any later change triggers the whole cycle again, ensuring continuous trust in all mutable data while keeping the registry fully decentralised.

### Endorsement Schema {#endorsement-schema}

An Endorsement attestation is a simple way for an organization to vouch for a DID subject..

### Certification Schema {#certification-schema}

A certification attestation is a more structured type of endorsement that reflects the typical certification model used by certification bodies such as the Federal Trade Commission, Common Criteria, FIPS, Underwriter Laboratories, SunSpec Alliance, and countless others.  In these programs there are four main actors:

* Product- the subject that is getting certification.  
* Developer- the entity responsible for the product.  
* Assessor- a trusted third party that analyzes the product against a requirements document and test procedure.  
* Certification Body- an entity (such as an industry consortium or government body) that creates and administers the certification program.

The use case flow using a certification schema is as follows:

1. Developer signs agreement with the Assessor to test the Product for certification.  
2. Assessor tests the product using documents ratified by the Certification Body.  
3. Assessor sends test results to the Certification Body.  
4. Certification Body reviews the test results.  If it passes, it signs the Certification attestation transaction.  If not, it sends the failure reasons to the Assessor.

### User Review Schema {#user-review-schema}

User reviews are critical for any app’s reputation.  The User Review attestation allows anyone to post a review on a DID.  

The problem with permissionless reviews is that a developer can review their own app by spinning up multiple wallets.  Users can also spam bad reviews on an app.  The User Review attestation relies on KYC and KYB attestations as well as app stores requiring these attestations to prevent these issues from making user reviews meaningless.  For example, an app store SHOULD only incorporate user review attestations created by wallets that have been verified (e.g.- KYC).  Applications MAY also require uniqueness attestations (e.g.- Worldcoin) as well to prevent sybil attacks.  See below for more app store guidance on leveraging attestations to protect users.

Specifications on the below schemas can be found in the [OMA3 Reputation Service draft proposal](https://docs.google.com/document/d/11NQgwzXkXMGXy07NIDcMovPjKQT4W3CiHWdNZ2TpbQ0/).

### Client Guidance {#client-guidance}

To ensure integrity and prevent user harm from fraudulent or misleading app metadata, all clients consuming data from OMATrust SHOULD adopt an attestation-based trust policy.

An app store SHOULD NOT display any application token unless the following conditions are met:

1. `dataUrl` resolves to valid JSON conforming to the current offchain schema.

2. The SHA-256 (or Keccak-256) hash of the fetched JSON exactly matches the on-chain `dataHash` for the token.

3. A valid third-party attestation has been published referencing that specific `dataHash` and using an accepted schema(s).

Each app store may define its own attestation trust model. For example:

| Model | Description |
| ----- | ----- |
| Single attester | A centralized store operator acts as sole reviewer (like Apple). |
| Multi-attester | Accept attestations from any approved third party (e.g., Mozilla, OMA3, community DAOs). |
| AI \+ human fallback | Require attestation from an automated reviewer unless the confidence score is low, in which case fallback to a human attester. |
| Community attestations | Support attestations from public reviewers with onchain reputation systems. |

If an application does not have a valid attestation:

* It SHOULD be hidden by default from public listings.

* It MAY be shown with a warning or behind a user-controlled setting (e.g., “Show unverified apps” toggle).

* It SHOULD NOT be eligible for ranking, recommendation, or featuring.

This approach balances decentralized publishing with user trust, enabling permissionless participation while minimizing abuse.

# Change History {#change-history}

| Version | Date | Comments |
| :---- | :---- | :---- |
| 0.1 |  | Initial draft \- Alfred Tom |
|  |  |  |
|  |  |  |

# **Appendix A — `did:artifact` (Provisional)** {#appendix-a-—-did:artifact-(provisional)}

**Status.** Provisional DID method used within this specification to identify **verifiable payloads** (binaries, containers, and website proof files such as SRI manifests or site snapshots). A standalone method spec and registry entry will follow.

## **A.1 Overview** {#a.1-overview}

`did:artifact` is a **content-addressable identifier**. The method-specific ID is a **CIDv1** (multibase base32-lower) whose **multihash** encodes the hash algorithm and digest of the artifact’s bytes.  
**V1 requirement:** the multihash **MUST** be **sha2-256** (32-byte digest).

* Binaries/containers: hash the **file/image bytes**.  
* Websites: hash a **proof artifact** (e.g., JCS-canonicalized SRI manifest JSON, or a site snapshot archive).  
* Each artifact gets its **own** DID; different files/proofs → different DIDs.

## **A.2 Identifier Syntax** {#a.2-identifier-syntax}

```
did:artifact:<cidv1>
```

*   
  `<cidv1>` **MUST** be CIDv1 encoded with multibase **base32-lower**.  
* The CID’s multihash **MUST** use **sha2-256** under this spec version.  
* The multicodec **SHOULD** be `raw` for opaque bytes. Using a more specific codec does not change verification semantics.

## **A.3 Computing `artifactDid`** {#a.3-computing-artifactdid}

**Common procedure (all artifact types):**

1. Obtain the exact **artifact bytes** (after any required canonicalization for that type).  
2. Compute **SHA-256** over those bytes.  
3. Wrap as **multihash** (function code \+ length \+ digest).  
4. Build **CIDv1** (multicodec `raw` unless specified otherwise).  
5. Multibase-encode (base32-lower) → prepend `did:artifact:`.

**Type notes:**

* **Binary / Installer / Archive:** use bytes **exactly as distributed** (no repacking). Apply common procedure.  
* **Container (OCI):** use the **OCI image manifest bytes** as stored in the registry (`application/vnd.oci.image.manifest.v1+json`). Apply common procedure.  
* **Website SRI manifest (JSON):** canonicalize with **JCS** (JSON Canonicalization Scheme), then apply common procedure to the canonical UTF-8 bytes.  
  * Paths in the manifest **MUST** be same-origin absolute paths (no query/fragment). Producer normalization: single percent-decode, Unicode NFC, collapse `//`, strip trailing `/` (except root), preserve case.  
* **Website snapshot (archive):** create a deterministic archive when possible (fixed owner/mode/timestamps). Apply common procedure to the archive bytes.

## **A.4 Verification (Client Requirements)** {#a.4-verification-(client-requirements)}

When a record references an `artifactDid`, verifiers **MUST**:

1. Fetch bytes from **any** location (HTTP(S), ipfs://, local, etc.).  
2. Recompute CIDv1(sha2-256) per A.3.  
3. **Require equality** with the referenced `artifactDid`. If different → invalid, regardless of URL or signature.

**Websites:**

* If the artifact is an **SRI manifest**, verifiers **SHOULD** also validate loaded assets against the per-file SRI hashes.  
* If the artifact is a **snapshot**, verifiers **MAY** present a “verified snapshot” badge when the content matches.

**No trust from URLs.** Links are advisory for discovery/distribution only.

## **A.5 Data Model Integration (normative hooks)** {#a.5-data-model-integration-(normative-hooks)}

* **`dataUrl.platforms`**  
  * `launchUrl` (**required**) is the user-facing deep link/store page.  
  * `artifactDid` (**optional**) **SHOULD** be present when a platform ships verifiable bytes (desktop installer/CLI/container) or a website proof artifact.  
  * If `artifactDid` is present, a matching entry **MUST** exist at `dataUrl.artifacts[artifactDid]`. See A.6.  
* **`dataUrl.artifacts`** (map keyed by `artifactDid`)  
  * **Key:** `did:artifact:<cidv1>` per this appendix.  
  * **Common fields:** `type` (`"binary"|"container"|"website"|...`), `sizeBytes?`, `distributionURIs[]?`, `signatureURIs[]?`, `sbomDid?`, `provenanceURI?`.  
  * **Type-specific:**  
    * binary/container: `os`, `arch`, optional `libc`, `variant`; container may include `ociDigest`.  
    * website: `originDid` (`did:web:<apex>`), and either inline `sriManifest` (the canonicalized JSON used for hashing) **or** pointers `manifestDid`/`snapshotDid` (both `did:artifact:*`).  
  * **Compatibility (optional):** `algo` (e.g., `"sha256"`), `digestHex` (lowercase); included for OCI/TUF/Sigstore tooling.

## **A.6 Policy (V1)** {#a.6-policy-(v1)}

* **Allowed hash:** only **sha2-256** is permitted for producing `did:artifact` values under this spec version. Verifiers **MUST** read the multihash algorithm but **MUST reject** non-permitted algorithms.  
* **Website scope:** website proof artifacts **MUST** be scoped to an apex origin; cross-origin redirects **MUST NOT**be followed during attestation/verification.  
* **Caching:** verifiers MAY cache computed CIDs; any new download **MUST** be re-verified.

## **A.7 Security Considerations** {#a.7-security-considerations}

* **Redirect/mirror safety:** content identity derives solely from `artifactDid`; mirrors/CDNs are acceptable.  
* **Dynamic content:** SRI manifests cover only listed assets; non-listed dynamic responses are **out of scope** and should be treated as unverified.  
* **Determinism:** prefer deterministic packaging to avoid unintentional hash churn.  
* **Keyed proofs:** signatures/SBOM/provenance strengthen trust but **do not replace** byte-level verification against `artifactDid`.  
* **DID Index Address semantics**: \`indexAddress(did)\` (see §{\#did-index-address}) is a deterministic \*\*indexing label\*\* for discovery and partitioning, not proof of control. Do \*\*not\*\* interpret it as a signer/owner; never send assets to it. The chance a real EOA equals this address is negligible (≈ 1 / 2^160), and the versioned prefix prevents cross-scheme overlap.

## **A.8 DID Index Address Helper** {#a.8-did-index-address-helper}

Reference Solidity helper for computing the Index Address from a \`didHash\`:

\`\`\`solidity

library DidIndex {

    /// @notice Compute the DID Index Address used for EAS recipient or other address-keyed indexes.

    /// @dev didHash \= keccak256(canonicalizeDID(did))

    function toAddress(bytes32 didHash) internal pure returns (address) {

        // Domain-separated, versioned prefix for portability and clarity.

        bytes32 h \= keccak256(abi.encodePacked("DID:Solidity:Address:v1:", didHash));

        return address(uint160(uint256(h)));

    }

}

\`\`\`

**Safety:** The returned address is an **index label**, not a controller. Never infer control or send assets to it. Always include `subjectDidHash` in payloads and verify consistency with the derived `recipient`.

## **A.9 Forthcoming Method Registration (informative)** {#a.9-forthcoming-method-registration-(informative)}

This appendix will be extracted into a standalone `did:artifact` method specification and registered in the **W3C DID Spec Registries**. The standalone spec will define a minimal DID Document and JSON-LD context (e.g., `https://w3id.org/did-artifact/v1`). Until then, this appendix is the normative definition for use within this specification.

## **A.10 Examples (informative)** {#a.10-examples-(informative)}

**Binary (Windows installer)**

```json
"platforms": {
  "windows": {
    "launchUrl": "myapp://open",
    "preferredDownloadUrl": "https://cdn.example.com/AppSetup.exe",
    "artifactDid": "did:artifact:<cidv1>"
  }
},
"artifacts": {
  "did:artifact:<cidv1>": {
    "type": "binary",
    "os": "windows", "arch": "x64",
    "sizeBytes": 73400320,
    "distributionURIs": [
      "https://cdn.example.com/AppSetup.exe",
      "ipfs://<cidv1>"
    ],
    "signatureURIs": ["https://sig.example.com/win.sig"],
    "algo": "sha256",
    "digestHex": "<sha256-hex>"
  }
}
```

**Website (SRI manifest)**

```json
"platforms": {
  "web": {
    "launchUrl": "https://play.example.com",
    "artifactDid": "did:artifact:<cidv1-of-jcs-sri-manifest>"
  }
},
"artifacts": {
  "did:artifact:<cidv1-of-jcs-sri-manifest>": {
    "type": "website",
    "originDid": "did:web:example.com",
    "sriManifest": {
      "version": 1,
      "origin": "example.com",
      "algo": "sha512",
      "hashEncoding": "base64",
      "paths": {
        "/assets/app.css": "sha512-…",
        "/assets/app.js":  "sha512-…"
      }
    }
  }
}
```

---

### **Editor’s note (remove before publishing)** {#editor’s-note-(remove-before-publishing)}

* Add “See Appendix A.2” references next to `artifactDid` mentions in `dataUrl.platforms` and `dataUrl.artifacts`.  
* When you create the external repo, move this appendix verbatim, add test vectors, and link back here.

**Appendix B**  
**Website Artifacts and SRI Manifests Draft Notes**

**Status:** Informative draft. Not required for v1 conformance. This appendix captures design intent for website verification to be finalized in v2.

## **W.1 Summary (design intent)** {#w.1-summary-(design-intent)}

* Website proofs will use **`artifactDid = did:artifact:<cidv1>`** (same as binaries).  
* The website proof artifact will be an **SRI manifest (JSON)** whose **JCS-canonicalized** bytes are hashed (SHA-256 → CIDv1) to form the `artifactDid`.  
* `platforms.web.launchUrl` remains the UX entry point; `platforms.web.artifactDid` (optional for v1) can point to the website proof artifact when available.

## **W.2 Planned artifact shape (non-normative)** {#w.2-planned-artifact-shape-(non-normative)}

```json
"artifacts": {
  "did:artifact:<cidv1-of-sri-manifest>": {
    "type": "website",
    "sriManifest": {
      "version": 1,
      "origin": "example.com",        // apex domain
      "algo": "sha512",
      "hashEncoding": "base64",
      "paths": { "/assets/app.js": "sha512-..." }
    },
    "downloadUrls": ["ipfs://<cidv1-of-sri-manifest>"] // optional
  }
}
```

**Notes:**

* The `"origin"` inside the manifest binds it to the domain and is part of the hashed bytes.  
* No separate `originDid` is required in v1 draft; the client already knows the domain via `platforms.web.launchUrl`.

## **W.3 Planned verification algorithm (non-normative)** {#w.3-planned-verification-algorithm-(non-normative)}

1. Resolve `artifactDid` from `platforms.web.artifactDid`.  
2. Obtain manifest bytes (inline `sriManifest` or fetch via `downloadUrls`).  
3. **JCS** canonicalize → **SHA-256** → **CIDv1**; require equality with `artifactDid`.  
4. Ensure `registrableDomain(platforms.web.launchUrl) == sriManifest.origin`.  
5. When rendering the site, validate loaded assets against manifest SRI entries.

## **W.4 Open items (track in issues)** {#w.4-open-items-(track-in-issues)}

* Deterministic packaging & test vectors for SRI manifests (JCS inputs/outputs).  
* Handling multi-origin assets (likely **out of scope** for v1 web verification).  
* Optional “snapshot” artifact (tar/warc) and how to embed origin metadata inside the archive if added later.  
* UX guidance for partial verification (some assets verified, others not).  
* Attestation linkage: publisher → domain (`ProofOfWebControl`) vs. publisher → artifact (`ArtifactBinding`).

**Appendix C: Recommended Trait Hashes**

This appendix provides a list of recommended trait strings for use in the on-chain \`traitHashes\` array. For the onchain field, developers must hash these values with keccak256.  Additional traits may be proposed via OMA3 governance processes.

| Trait String | Description |
| ----- | ----- |
| "api:openapi" | Include this if the interface field has a value of 2 and the API format is OpenAPI. |
| "api:graphql" | Include this if the interface field has a value of 2 and the API format is GraphQL. |
| “api:jsonrpc” | Include this if the interface field has a value of 2 and the API format is JSON-RPC. |
| "api:mcp" | Include this if the interface field has a value of 2 and the API format is OpenAPI. |
| "api:a2a" | Include this if the interface field has a value of 2 and the API format is A2A. |
| "token:erc20" | Include this if the token in the fungibleTokenId field supports ERC-20. |
| “token:erc3009” | Include this if the token in the fungibleTokenId field supports ERC-3009. |
| “token:spl” | Include this if the token in the fungibleTokenId field supports SPL. |
| “token:2022 | Include this if the token in the fungibleTokenId field supports Token-2022. |
| "token:transferable" | Include this if the token in the fungibleTokenId is transferable. |
| "token:burnable" | Include this if the token in the fungibleTokenId is burnable. |
| “pay:x402” | Include this if the endpoint supports x402 payments. |

**Appendix D**  
**Example Test Vectors for Canonicalization+Hashing**

This appendix provides example test vectors to guide implementers in ensuring correct canonicalization and hashing of JSON data for the \`dataHash\` field. Implementers SHOULD compute hashes using the algorithm specified in \`dataHashAlgorithm\` (e.g., sha256 or keccak256) and verify consistency across runtimes (e.g., Node.js, Python, Solidity). Clients may use these vectors to validate their implementations. Additional vectors should be published by implementers to cover edge cases.

| Input JSON | Canonicalized JSON | Expected Hash (Compute per dataHashAlgorithm) |
| ----- | ----- | ----- |
| {"a": 1.0, "b": {"c": "\\n"}} | {"a":1,"b":{"c":"\\n"}} | \[Compute Hash\] |
| {"b":2,"a":1} | {"a":1,"b":2} | \[Compute Hash\] |
| {"x":\[{"y":true}\]} | {"x":\[{"y":true}\]} | \[Compute Hash\] |

