# **Republic OMA Tokenomics**

## **Introduction**

This document provides an overview of the OMA token for Republic investors, including OMA supply, distribution, unlock mechanics, and value accrual. It is intended as a companion to the OMATrust Whitepaper, which describes the mission of OMA3 and the role of OMAChain and OMATrust in building credibly neutral infrastructure for the open internet.  

This paper focuses specifically on token design and mechanics. It explains how the token is issued, how it is used within the system, how value flows back to the token, and how supply is managed through allocation and unlock schedules.  For a full technical overview of OMATrust and OMAChain, refer to the OMATrust Whitepaper, which focuses solely on the protocol's architecture and mission.

## **1\. Token Overview**

* **Token Name:** OMA  
* **Total Supply:** 333,333,333 (fixed, non-inflationary).  
* **Issuance:** Tokens are minted on Ethereum mainnet for security. To be used as gas on OMAChain, tokens must be bridged through the native OMAChain bridge.  
* **Primary Function:** OMA is the native gas token of OMAChain.  
* **OMATrust Incentives:** The token is also used to incentivize adoption of OMATrust across chains.  
* **Direct Sale on OMAChain:** OMA can be sold directly to users through a front-end on OMAChain. Tokens purchased in this way may only be used to pay for gas fees on OMATrust contracts operating on OMAChain.  
* **Incentive Alignment:** The token provides a mechanism to reward contributions to shared infrastructure, addressing the underfunding problem common in open source and consortia-based projects.

## **2\. Value Accrual**

OMA tokens accrue value through two primary mechanisms:

1. **Transaction Demand**  
   * OMA is required to transact on OMAChain.  
   * As demand for transactions increases, demand for OMA increases.  
2. **Reading Data Revenue**  
   * High volume reads of OMATrust data generates revenue for OMA3.  
   * If allowed in the future by regulatory clarity, revenue from OMATrust can be distributed directly to token holders.

The scale of revenues is discussed further in Section 7 (Market Opportunity).

## **3\. Distribution**

The total supply of OMA tokens is fixed at **333,333,333**. Tokens are allocated across five categories: Community & Contributors, Investor Tranche, Founders, Treasury, and Liquidity & Market Operations.

The **Investor Tranche** includes the Republic sale and potential future sales to external participants. For regulatory compliance, this tranche is subject to lockups when tokens are transferred. The Republic sale represents the first allocation of tokens in this category.

The **Community & Contributor** allocation rewards OMA3 members for past and future contributions. This includes tokens already distributed, such as the Season 1 airdrops, as well as future distributions for work on standards, infrastructure, and governance.

The **Founder** allocation was distributed to the eight founders of OMA3. These tokens are subject to multi-year vesting with cliffs.

The **Treasury** is held in a multi-signature wallet controlled by the OMA3 Board of Directors, which currently consists of eight directors. Treasury tokens are not locked at TGE. Tokens may only be released following a vote of the Board and the required multi-sig approvals. This ensures that treasury usage is collectively governed and transparent.

The **Liquidity & Market Operations** allocation is reserved to ensure sufficient liquidity on exchanges and bridges and to support the functioning of the OMA token in secondary markets.

## **4\. Unlocks**

Lockups are applied to several token allocations to align with the spirit of U.S. securities regulations, in particular Rule 144\. OMA3’s U.S. counsel, **Zuber Lawler**, has advised that OMA itself should not be treated as a security. However, transactions involving OMA may, under certain interpretations, be subject to Rule 144 restrictions. To ensure compliance and reduce regulatory risk, OMA3 applies one-year lockups to categories of tokens distributed directly by OMA3.

* The **Investor Tranche**, including the Republic sale and any future sales, is subject to a one-year lock beginning on the date of transfer. After the one-year period, tokens vest linearly over nine months.  
* The **Community & Contributor** allocation is also subject to a one-year lock. After the lock period, these tokens vest on a **24-month linear schedule**. The Season 1 airdrop occurred more than one year ago, so the regulatory lock requirement has already been satisfied. However, these tokens remain subject to the additional restrictions of the **Builder Program** (see Section 5).  
* The **Founder** allocation was distributed to the eight founders of OMA3 more than one year ago. The Rule 144 lock requirement has therefore been satisfied, but these tokens are also governed by the **Builder Program** restrictions.  
* The **Liquidity & Market Operations** allocation is not subject to lockups.

All unlocks are enforced by onchain vesting contracts. The treasury multi-sig has limited authority to adjust lock dates or vesting schedules, and all activity is visible onchain.

## **5\. Builder Program**

The Builder Program governs the Season 1 tokens distributed to OMA3 members and founders. While the one-year Rule 144 lock has already been satisfied for these allocations, additional restrictions apply to ensure that early tokens support ecosystem development rather than speculative selling.

Under the Builder Program:

* **Monthly Allowance:** Each participating wallet may sell up to **5,000 OMA per month** without restriction.  
* **Reinvestment Requirement:** Sales above this threshold are only permitted if the proceeds are **reinvested into approved Builder Activities**.  
* **Approved Builder Activities:** These include application and infrastructure development, standards contributions, liquidity provisioning (including alternative pools), and other ecosystem growth initiatives recognized by OMA3.  
* **Governance Oversight:** The program applies to a defined set of \~**10 wallets** that begin their 24-month linear vesting at TGE. This small number of participants makes oversight manageable. Compliance is monitored by the OMA3 Board of Directors, and members must provide evidence of reinvestment when selling above the monthly allowance.  
* **Program Duration:** The Builder Program will remain in effect at least until the Republic tokens begin their vesting schedule.

Future **Ecosystem Fund** distributions (previously referred to as Community & Contributor allocations) will not be subject to these restrictions, but will follow the general lock and vesting schedules outlined in Section 4\.

## **6\. Use of Funds**

Proceeds from the Republic raise will be applied to launch-critical expenses and protocol operations. OMA3 is a going concern and covers consortium operations through membership fees; therefore, Republic proceeds are directed primarily toward OMAChain and OMATrust launch, audits, liquidity, and developer support.

### **6.1 Allocation of Proceeds**

| Category | Amount | Notes |
| ----- | ----- | ----- |
| Member Launch Loan Payback | $25k | Infrastructure |
| Infrastructure costs for 6 months | $30-60k | Infrastructure |
| ETH Treasury (for settlement fees) | $20k | Bridge / settlement ops |
| Initial Uniswap v3 USDC POL | $50k | Protocol-owned liquidity |
| Additional Uniswap USDC POL | $100k | Liquidity expansion |
| Market Maker | $35k | Liquidity management |
| OMA3 Smart Contract Audits | $25k | Security audit |
| L2 \+ Native Bridge Audit | $15k | Security audit |
| Developer Marketing (docs, video, newsletters) | $30k | Ecosystem growth |

* **Milestone 1 subtotal:** ≈ $330k  
  * \~$180k in expenses  
  * \~$150k in protocol-owned liquidity (POL)  
* **Minimum required to launch:** ≈ $75k in proceeds  
* **Projected ongoing burn after initial 6 month mainnet launch:** ≈ $5-10k/month (ops \+ infra \+ security monitoring).

### **6.2 Notes**

* Republic will deduct its standard percentage fee from total proceeds raised.  
* OMA3 covers its own organizational and governance costs through **membership fees**, separate from this token sale.

## **7\. Market Opportunity**

OMATrust’s revenue model is based on **per-call trust checks**, turning verification into a scalable utility for the open internet. Based on the assumptions below, the 2030 Total Addressable Market (TAM) is approximately **$1.1 billion annually**:

* **Interactions:** \~5 billion humans making 100 calls/day \= 182.5 trillion calls/year. \~5 billion bots and AI agents making 500 calls/day \= 912.5 trillion calls/year. Combined total \= **1.095 quadrillion calls/year**.  
* **Trust Check Rate:** 1% of interactions require verification (e.g., initial access, refreshes, updates) \= **10.95 trillion checks/year**.  
* **Pricing:** Each check priced at **$0.0001**.  
* **Revenue:** 10.95 trillion checks × $0.0001/check \= **$1.095 billion/year**.

Like most blockchains, write operations such as token transfers and developer registrations require transaction “gas” fees, and these transaction fees are paid in the native token of the blockchain.  The more write transactions the more demand for the native token, which puts upward pricing pressure on the token (OMA in the case of OMAChain).  On OMAChain some write operations, such as user reviews, are **subsidized at no cost** to encourage usage of OMATrust.  Unlike most blockchains, OMA is also required to pay for reading data off of OMATrust.  So, the above revenue TAM needs to be paid in OMA, which puts substantially more upward pricing pressure on OMA than write transactions.  

In the future, if there is regulatory clarity, it may be possible to distribute OMATrust protocol revenue directly to OMA token holders, thereby increasing the inherent value of the token.

**Note:** These assumptions are illustrative and should not be treated as forecasts. Readers are encouraged to substitute their own assumptions about adoption rates, verification frequency, and pricing as part of their own analysis.

## **8\. Risks and Safeguards**

### **Regulatory Risk**

OMA3’s U.S. counsel, **Zuber Lawler**, has advised that OMA should not be treated as a security. OMA3’s Swiss counsel, **MME**, has provided guidance to ensure compliance under Swiss law. Despite this, regulators may determine that certain transactions involving OMA are securities transactions.  OMA3 address this with:

* One-year Rule 144 lockups applied to all direct transfers.  
* Ongoing legal guidance from both U.S. and Swiss counsel.  
* Conservative compliance measures embedded in unlock and vesting rules.

### **Market Risk**

OMA may experience price volatility, limited liquidity, or disorderly markets at launch.  To address this risk:

* The market maker manages all sales during the first month after TGE.  
* Member unlocks do not begin until one month after TGE.  
* Very limited member unlocks occur in the first year.  
* Vesting schedules stage token releases over time.  
* Liquidity provisioning is an approved Builder Activity.

### **Governance Risk**

As a Swiss Verein, OMA3 is governed by its members through an eight-person Board of Directors. Governance risk arises if treasury or protocol parameters are mismanaged.  To mitigate this risk:

* Swiss Verein governance structure distributes authority across members.  
* Multisig treasury control prevents unilateral action.  
* Onchain records provide transparency of treasury decisions.  
* Anyone can join OMA3 and participate in governance, further reducing risk.

### **Technical Risk**

OMAChain and OMATrust rely on smart contracts and bridges, which carry technical risk. However:

* Token issuance occurs on Ethereum mainnet to leverage its security.  
* Contracts are audited by trusted third parties.  
* Multi-sig control governs upgrades.  
* OMATrust does not hold total value locked (TVL) so there are no large honeypots to attack.  
* Phased deployment reduces the blast radius of new features.

### **Adoption Risk**

Token demand depends on adoption of OMAChain and OMATrust.  Chains often launch with great technology yet are met with muted adoption.  OMA3’s structure and token model are designed specifically to address this risk:

* The Ecosystem Fund is designed to incentivize adoption as well as contribution.  
* OMA3 members are already committed to integrating OMATrust, solving the “cold start” problem.

### **Slow Decisionmaking Risk**

Decentralized governance can take more time to reach consensus, raising concerns about decision-making efficiency. However:

* Neutral governance also tends to encourage quicker adoption than centralized control because participants favor openness over proprietary.

### **Innovative Legal Structure Risk**

OMA3 is structured as an association/consortium, not a traditional VC-backed lab. While this model is unusual in crypto (for now), it brings several benefits:  

* The association structure strengthens regulatory credibility, especially decentralization metrics.  
* It is more efficient than the typical lab\<\>foundation model that often suffers from misalignment.  
* It encourages more organizations to commit extensive resources to the project.  
* It is more credibly neutral at all governance layers.

### **Macro Risk**

Wider macroeconomic conditions and crypto market downturns could negatively affect OMA’s price and adoption.  As a safeguard:

* OMA3 maintains a conservative treasury and unlock structure to weather market cycles.

### **Competition Risk**

A larger or competing consortium could emerge with similar goals.  However:

* OMA3’s credibly neutral governance positions it as the “big tent” that can attract broad ecosystem support.  If a credibly neutral organization already exists it is rare that a competing one pops up.

## **9\. Disclaimer**

This Tokenomics Paper is provided for informational purposes only. It is not intended to constitute financial, legal, or investment advice, and should not be relied upon as such. Nothing in this document constitutes an offer to sell or the solicitation of an offer to purchase OMA tokens or any other security.

Participation in any token sale, including through Republic, will be governed solely by the terms and disclosures of the relevant sale platform. OMA3 makes no representations or warranties as to the accuracy or completeness of the information in this paper, which is subject to change without notice.