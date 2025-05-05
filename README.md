# Reserve Asset Token (RAT)
![Trusted in All Chains](https://img.shields.io/badge/Trusted-in%20All%20Chains-blueviolet?style=for-the-badge&logo=ethereum)

**Reserve Asset Token (RAT)** is a decentralized digital wealth system backed by real-world assets:  
- 🟡 Gold (RAU)  
- ⚪ Silver (RAG)  
- 💎 Diamond (RDI)  
- ❤️ Ruby (RRU)  
- 🔷 Sapphire (RSA)  
- 🪙 Platinum (RPT)  

Built on Ethereum as ERC-20 tokens, RAT tokens are pegged to 2025 asset prices and offer a store of value, investment opportunity, and ESG-compliant wealth preservation. This project supports multi-chain payments via NOWPayments, integrates Chainlink for real-time price feeds, and plans to incorporate StoneAlgo for diamond pricing.

---

## 📚 Table of Contents

- [Project Overview](#project-overview)  
- [Tokenomics](#tokenomics)  
- [Smart Contracts](#smart-contracts)  
- [Wallet Setup](#wallet-setup)  
- [Website Integration](#website-integration)  
- [Deployment](#deployment)  
- [External Integrations](#external-integrations)  
- [Development Setup](#development-setup)  
- [Security](#security)  
- [Future Enhancements](#future-enhancements)  
- [Contributing](#contributing)  
- [License](#license)  

---

## 🪙 Project Overview

RAT consists of **six ERC-20 tokens**, each representing a tangible asset with a fixed supply:

| Token | Asset     | Symbol | Supply     |
|-------|-----------|--------|------------|
| 🟡    | Gold      | RAU    | 3,510,000  |
| ⚪    | Silver    | RAG    | 2,630,000  |
| 💎    | Diamond   | RDI    | 1,750,000  |
| ❤️    | Ruby      | RRU    | 880,000    |
| 🔷    | Sapphire  | RSA    | 880,000    |
| 🪙    | Platinum  | RPT    | 350,000    |

**Total Supply:** 10,000,000 tokens  
**Pricing Source:** [Metals-API (2025)](https://metals-api.com)  
**Key Mechanics:**
- 20% Annual Vesting (5 years)
- 0.5% Transaction Fee (distributed to stakers/liquidity)
- 2–5% APY via Staking

---

## 📊 Tokenomics

### 📈 Distribution and Valuation (2025)

| Asset     | Symbol | Price (USD/oz) | Market Value (USD)     |
|-----------|--------|----------------|-------------------------|
| Gold      | RAU    | $3,268.07      | $11,470,925,670         |
| Silver    | RAG    | $32.1622       | $84,586,586             |
| Diamond   | RDI    | $1.00 (spec.)  | $1,750,000              |
| Ruby      | RRU    | $1.00 (spec.)  | $880,000                |
| Sapphire  | RSA    | $1.00 (spec.)  | $880,000                |
| Platinum  | RPT    | $951.6604      | $333,081,140            |
| **Total** |        |                | **$11,892,103,396**     |

**Projected Value (2030):** ~$16 Billion  
**Growth Rates:**  
- Gold: +6%/yr  
- Silver: +6.5%  
- Platinum: +10%  
- Diamond: +7.5%  
- Ruby/Sapphire: +8%

---

## 🧠 Smart Contracts

### 🔐 Core Contracts

- `RAUGold.sol`, `RAGSilver.sol`, `RDIDiamond.sol`, `RRURuby.sol`, `RSASapphire.sol`, `RPTPlatinum.sol` — ERC-20 token logic
- `RATController.sol` — Manages vesting, staking, pricing, purchasing

### 🔑 Key Functions

- `purchaseTokens(token, amount, useUSDT)`
- `releaseTokens(token)`
- `stake(token, amount)`
- `unstake(token)`
- `getTokenPrice(token)`

### 📦 Dependencies

- OpenZeppelin (ERC-20, Ownable, ReentrancyGuard)
- Chainlink Price Feeds (XAU/USD, XAG/USD, XPT/USD)

---

## 🔐 Wallet Setup

**Recommended Wallets:**
- MetaMask
- Trust Wallet
- Coinbase Wallet
- Ledger / Trezor
- Exodus / Coinomi

**Setup Instructions:**
1. Install wallet (e.g., MetaMask)
2. Configure Ethereum Mainnet:
   - Chain ID: `1`
   - RPC: `https://mainnet.infura.io/v3/YOUR_PROJECT_ID`
3. Fund wallet with ETH & stablecoins
4. Add custom tokens using contract addresses post-deployment

---

## 🌐 Website Integration

**File Structure:**
```

reserve-asset/
├── public/
│   ├── index.html, assets.html, payments.html, faq.html
│   ├── js/: web3.min.js, ethers.js, main.js
├── server/
│   └── index.js
├── contracts/
│   └── All .sol contracts
├── scripts/
│   └── deploy.js

````

**UI Features:**
- Live asset prices (via `getTokenPrice`)
- Token staking/purchase
- Pie chart of supply
- Multi-chain payments (NOWPayments)

---

## 🚀 Deployment

### Prerequisites

- Node.js, npm
- Hardhat:  
  `npm install --save-dev hardhat`
- Infura/Alchemy RPC
- Ethereum wallet with ETH

### `hardhat.config.js`

```js
require("@nomiclabs/hardhat-ethers");
module.exports = {
  solidity: "0.8.20",
  networks: {
    mainnet: {
      url: "https://mainnet.infura.io/v3/YOUR_PROJECT_ID",
      accounts: ["YOUR_PRIVATE_KEY"]
    },
    sepolia: {
      url: "https://sepolia.infura.io/v3/YOUR_PROJECT_ID",
      accounts: ["YOUR_PRIVATE_KEY"]
    }
  }
};
````

### Commands

```bash
npx hardhat compile
npx hardhat run scripts/deploy.js --network mainnet
npx hardhat verify --network mainnet <CONTRACT_ADDRESS> <ARGS>
```

---

## 🔗 External Integrations

* **Chainlink**:

  * Price Feeds: XAU/USD, XAG/USD, XPT/USD
* **NOWPayments**:

  * Multi-chain crypto payment API
* **StoneAlgo (Planned)**:

  * Chainlink External Adapter for Diamonds
  * Fallback: Manual price update

---

## 🧑‍💻 Development Setup

### Install Dependencies

```bash
npm install @openzeppelin/contracts @chainlink/contracts ethers web3 chart.js
```

### Local Development

```bash
npx hardhat node
npx hardhat run scripts/deploy.js --network localhost
```

### Sample Test (Jest/Chai)

```js
describe("RATController", () => {
  it("Should purchase RAU tokens", async () => {
    const RAUGold = await ethers.getContractFactory("RAUGold");
    const rau = await RAUGold.deploy();
    const controller = await (await ethers.getContractFactory("RATController")).deploy(
      rau.address, /* other token addresses */
      "USDT_ADDRESS", "USDC_ADDRESS", "XAU_FEED", "XAG_FEED", "XPT_FEED"
    );
    // Add assertions
  });
});
```

---

## 🛡️ Security

* **Audits**: Planned with CertiK or Trail of Bits
* **Best Practices**:

  * `ReentrancyGuard`, `Ownable`, `Pausable`
  * Validate Chainlink prices
  * Restrict sensitive functions
  * Encourage secure wallet usage

---

## 📈 Future Enhancements

* 🧬 StoneAlgo price feed via Chainlink
* 📉 Ruby/Sapphire: Gemval/Rapaport pricing
* ⛓ Layer-2 deployment (Optimism/Arbitrum)
* 🌉 Cross-chain bridges (e.g., Wormhole)
* 🗳 DAO governance (OpenZeppelin Governor)

---

## 🤝 Contributing

```bash
# Fork the repo and create a branch
git checkout -b feature/my-feature

# Make changes and commit
git commit -m "Add feature"

# Push and open a PR
git push origin feature/my-feature
```

---

## 📄 License

This project is licensed under the [MIT License](./LICENSE).
