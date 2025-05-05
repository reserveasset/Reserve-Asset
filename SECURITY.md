# 🛡 SECURITY POLICY

## Overview

The **Reserve Asset Token (RAT)** project is a decentralized digital wealth system comprising six ERC-20 tokens (RAU, RAG, RDI, RRU, RSA, RPT) backed by real-world assets (Gold, Silver, Diamond, Ruby, Sapphire, Platinum). Built on Ethereum, the project prioritizes security to protect users, developers, and the ecosystem. This document outlines security measures, best practices, vulnerability reporting, and planned audits for the smart contracts, website, and external integrations.

## Table of Contents

1. [Security Principles](#security-principles)
2. [Smart Contract Security](#smart-contract-security)
3. [Website Security](#website-security)
4. [User Security](#user-security)
5. [External Integrations](#external-integrations)
6. [Vulnerability Reporting](#vulnerability-reporting)
7. [Planned Security Audits](#planned-security-audits)
8. [Incident Response](#incident-response)
9. [Contact](#contact)

---

## Security Principles

1. **Defense in Depth**: Multiple layers of security for contracts, website, and integrations.
2. **Least Privilege**: Restrict access to sensitive functions and data.
3. **Transparency**: Publish contract code, verification details, and security practices.
4. **Proactive Monitoring**: Continuously monitor for vulnerabilities and attacks.
5. **User Education**: Guide users on secure wallet usage and phishing prevention.

---

## Smart Contract Security

The RAT project includes six ERC-20 token contracts (RAUGold.sol, RAGSilver.sol, RDIDiamond.sol, RRURuby.sol, RSASapphire.sol, RPTPlatinum.sol) and a controller contract (RATController.sol) managing vesting, staking, pricing, and purchases.

### Measures

* **Audited Libraries**: Use OpenZeppelin’s audited contracts for ERC-20, Ownable, ReentrancyGuard, and Pausable.
  Version: @openzeppelin/contracts\@4.9.3.

* **Reentrancy Protection**:
  ReentrancyGuard applied to purchaseTokens, stake, and unstake functions in RATController.sol.

* **Access Control**:
  Sensitive functions (releaseTokens, updateSpeculativePrice) restricted to the contract owner via Ownable.
  Future: Transition to a DAO for decentralized governance.

* **Price Feed Validation**:
  Chainlink Price Feeds (XAU/USD, XAG/USD, XPT/USD) validated for positive and recent prices in `getPrice`.
  Example: `require(price > 0, "Invalid price")`.

* **Pausability**:
  Pausable modifier available for emergency stops in case of detected vulnerabilities.

* **Gas Optimization**:
  Avoid unbounded loops and complex computations to prevent denial-of-service attacks.

* **Testing**:
  Unit tests with Hardhat cover token minting, vesting, staking, and purchases.
  Example: Test purchaseTokens for correct USDT/USDC transfers and 0.5% fee.

* **Code Review**:
  Internal reviews by the development team.
  External audits planned (see Planned Security Audits).

### Known Risks

* **Oracle Manipulation**: Chainlink Price Feeds could be compromised, though mitigated by their decentralized nature.
* **Speculative Prices**: Diamond (RDI), Ruby (RRU), and Sapphire (RSA) use manual prices (\$1.00/oz), updatable only by the owner.
* **Owner Privileges**: Centralized control of vesting and price updates (to be decentralized via DAO).

### Best Practices

* Minimize contract complexity.
* Use events (TokensPurchased, TokensReleased, Staked, Unstaked) for transparency.
* Verify contracts on Etherscan post-deployment.

---

## Website Security

The RAT website (index.html, assets.html, prices.html, payments.html, faq.html, etc.) uses Web3.js/Ethers.js for wallet interactions and Chart.js for visualizations.

### Measures

* **HTTPS**: Enforce HTTPS to encrypt data in transit.

* **Content Security Policy (CSP)**:
  Restrict script sources to trusted CDNs (e.g., Web3.js, Ethers.js) and local files.
  Example: `Content-Security-Policy: script-src 'self' https://cdn.ethers.io;`.

* **Input Validation**:
  Sanitize user inputs in `payments.html` (e.g., token amount) to prevent XSS.
  Example: Validate amount as a positive number before calling `purchaseTokens`.

* **Wallet Interaction**:
  Use MetaMask’s `eth_requestAccounts` for secure wallet connections.
  Avoid storing private keys or sensitive data client-side.

* **Dependency Management**:
  Pin versions for Web3.js, Ethers.js, and Chart.js to avoid supply chain attacks.
  Example: `npm install ethers@5.7.2`.

* **CSRF Protection**:
  No server-side sessions, but future backend (e.g., NOWPayments callback) will use CSRF tokens.

* **Phishing Prevention**:
  Publish official website URL on verified channels (e.g., GitHub, Twitter).
  Warn users to verify URLs in `faq.html`.

### Known Risks

* **Phishing**: Users may visit fake websites mimicking RAT.
* **Frontend Exploits**: Malicious scripts could manipulate Web3.js calls if CSP is bypassed.
* **Wallet Connection**: Users may approve malicious transactions if misled.

### Best Practices

* Regularly update dependencies (npm update).
* Monitor for phishing domains and report to registrars.
* Educate users on verifying contract addresses (via Etherscan).

---

## User Security

Users interact with RAT via Ethereum-compatible wallets (MetaMask, Trust Wallet, Coinbase Wallet, Ledger, Trezor) to receive, purchase, and stake tokens.

### Measures

* **Wallet Guidance** (in `faq.html`):
  Instruct users to secure seed phrases offline.
  Recommend hardware wallets (Ledger, Trezor) for large holdings.
  Example: “Never share your seed phrase or private key.”

* **Transaction Safety**:
  Display estimated gas fees (\~\$5–\$60) in `payments.html` before transactions.
  Warn users to verify contract addresses before approving USDT/USDC transfers.

* **Phishing Warnings**:
  `faq.html` advises checking website URLs and avoiding unsolicited links.

* **Education**:
  Explain risks of smart contract interactions and market volatility in `faq.html`.

### Known Risks

* **Seed Phrase Theft**: Users may fall for phishing or malware.
* **Transaction Errors**: Approving incorrect contracts or amounts.
* **Gas Fee Volatility**: High Ethereum gas fees may surprise users.

### Best Practices

* Use reputable wallets with strong security (e.g., MetaMask’s latest version).
* Double-check transaction details (e.g., RATController address).
* Monitor wallet activity via Etherscan.

---

## External Integrations

RAT integrates with Chainlink, NOWPayments, and plans for StoneAlgo.

### Chainlink

* **Security**:
  Use mainnet Price Feeds (XAU/USD: `0x214eD9Da11D2fbe4650a47fF70e16778e4967391`, XAG/USD, XPT/USD) from Chainlink’s decentralized oracles.
  Validate price data for freshness and positivity.

* **Risks**:
  Oracle downtime or manipulation (low probability due to Chainlink’s robustness).

* **Mitigation**:
  Fallback to last known price or pause contract if oracle fails.

### NOWPayments

* **Security**:
  Secure API key storage in backend (`server/index.js`).
  Use HTTPS for payment callbacks.
  Validate callback data to prevent spoofing.

* **Risks**:
  Callback manipulation or payment processing errors.

* **Mitigation**:
  Implement signature verification for NOWPayments callbacks.
  Log all transactions for auditing.

### StoneAlgo (Future)

* **Security**:
  Plan: Develop Chainlink External Adapter for Diamond prices (e.g., \$5,000/carat ≈ \$708,645/oz).
  Secure API calls with authentication (if StoneAlgo provides an API).

* **Risks**:
  Unreliable price data or API downtime.

* **Mitigation**:
  Use manual price updates (`updateSpeculativePrice`) until adapter is live.
  Validate fetched prices against market ranges.

---

## Vulnerability Reporting

We encourage responsible disclosure of security vulnerabilities.

### Process

* **Report**:
  Email vulnerabilities to [security@reserveasset.io](mailto:security@reserveasset.io).
  Include details: description, impact, reproduction steps, and suggested fix.

* **Response**:
  Acknowledgment within 48 hours.
  Investigation and resolution timeline based on severity.

* **Rewards**:
  Bug bounty program (TBD) for critical vulnerabilities.
  Public acknowledgment (with permission) for responsible disclosures.

### Severity Levels

* **Critical**: Exploits enabling token theft, contract manipulation, or user fund loss.
* **High**: Bugs affecting vesting, staking, or pricing logic.
* **Medium**: Frontend vulnerabilities (e.g., XSS) or minor contract issues.
* **Low**: Non-exploitable issues or documentation errors.

---

## Planned Security Audits

* **Auditors**: CertiK, Trail of Bits, or OpenZeppelin (TBD).
* **Scope**:

  * Smart contracts: RAUGold.sol, RATController.sol, etc.
  * Website: Web3.js/Ethers.js integrations, input validation.
  * Backend: NOWPayments callback logic.
* **Timeline**: Pre-mainnet deployment (Q2 2025, tentative).
* **Post-Audit**:
  Publish audit reports on the website (whitepaper.html).
  Address all findings before launch.

---

## Incident Response

### Process

* **Detection**:
  Monitor Etherscan, Chainlink, and user reports for anomalies.
  Use tools like Tenderly for real-time contract monitoring.

* **Containment**:
  Pause contracts using Pausable if a vulnerability is exploited.
  Notify users via website, Twitter, and Discord.

* **Resolution**:
  Deploy patched contracts or hotfixes.
  Compensate affected users (if applicable, per governance).

* **Post-Mortem**:
  Publish a detailed report on the incident, root cause, and fixes.
  Update this SECURITY.md with new measures.

### Communication

* **Channels**: Website banner, Twitter (@ReserveAsset), email to users.
* **Transparency**: Share incident details and resolution steps promptly.

---

## Contact

For security inquiries, contact:
Email: [security@reserveasset.io](mailto:security@reserveasset.io)
GitHub: Report issues at [github.com/reserve-asset/rat](https://github.com/reserve-asset/rat)
Community: Join our [Discord](#) or [Telegram](#).
