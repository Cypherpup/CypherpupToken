# Cypherpup (CPHP) Token - Secure, Fee-Optimized ERC20 Token

![Cypherpup Logo](hhttps://www.cphp.ai/_next/image?url=%2Fimages%2Fmadogi.png&w=750&q=75&dpl=dpl_9kiWjELC6ZizLQd65QPfYqc6Pa8x)  
*A next-generation meme coin built on Cypherium for lightning-fast transactions, cross-chain utility and DeFi integration.*

---

## 📜 Table of Contents
- [Project Overview](#-project-overview)
- [Key Features](#-key-features)
- [Tokenomics](#-tokenomics)
- [Fee Structure](#-fee-structure)
- [Security Mechanisms](#-security-mechanisms)
- [Burn Mechanics](#-burn-mechanics)
- [DEX Integration](#-dex-integration)
- [Ownership & Control](#-ownership--control)
- [Deployment & Setup](#-deployment--setup)
- [Important Notes](#-important-notes)
- [License](#-license)

---

## 🚀 Project Overview
**Cypherpup (CPHP)** is an ERC20 token featuring dynamic fee adjustments, liquidity protections, and anti-bot mechanisms. Designed for decentralized exchanges (DEXs), it implements:
- Adaptive buy/sell taxes (initially 0% customizable post-launch)
- Automatic ETH swaps for collected fees
- Transaction cooldowns and wallet limits
- Burn mechanics to reduce supply

---

## 🌟 Key Features

### **Tokenomics**
| Metric                | Value                   |
|-----------------------|-------------------------|
| Total Supply          | 50,000,000,000 CPHP     |
| Decimals              | 18                      |
| Initial Distribution  | - 3% Dev Wallet<br>- 7% Marketing<br>- 90% Liquidity | (iCan be adjusted as needed pre launch)

### **Dynamic Fees**
- **Buy Tax**: 0% (adjustable by `_taxWallet`)
- **Sell Tax**: 0% (adjustable by `_taxWallet`)
- Fee proceeds partially burned (1% of sell tax, 5% of buy tax) - (Can be adjusted accordingly post launch/Pre launch)

### **Security**
- 🛡️ **Cooldown Period**: 30-second restriction post-trading launch
- ⏳ **Transfer Delay**: 1 transaction/block per address
- 🤖 **Bot Blacklist**: Manual address blocking
- 🔒 **Max Limits**: 
  - `_maxTxAmount`: 2% of supply
  - `_maxWalletSize`: 2% of supply (removable via `removeLimits()`)

---

## ⚙️ Technical Breakdown

### **Burn Mechanism**
```solidity
function burn(address from, uint256 amount) public {
    require(msg.sender == from, "Unauthorized burn");
    _transferStandard(from, address(0), amount);
}
```
- Users can voluntarily burn tokens
- Automatic burns during fee processing

### **Fee Processing**
```solidity
function _takeBuyFees(/*...*/) private returns (uint256) {
    uint256 tAmount = amount.mul(taxRate).div(100);
    _transferStandard(from, _taxWallet, tAmount);
    burnFromTSupply(_taxWallet, burnPortion); 
}
```
- Fees collected in CPHP
- Auto-swapped to ETH via `swapTokensForEth()`
- ETH sent to `_taxWallet`

---

## 👨💻 Ownership & Control

### **Privileged Functions**
| Function               | Access     | Description                     |
|------------------------|------------|---------------------------------|
| `openTrading()`         | Owner      | Enables DEX trading             |
| `addBots()/delBots()`   | Owner      | Manage bot blacklist            |
| `reduceFees()`          | Tax Wallet | Adjust buy/sell taxes           |
| `manualSwap()`          | Tax Wallet | Force fee conversion to ETH     |

### **Wallet Setup**
```solidity
constructor(
    address taxWallet,
    address devWallet,
    address marketingWallet,
    address liquidityWallet
) {
    // Initial allocations
    _balances[_devWallet] = _tTotal.mul(3).div(100);
    _balances[_marketingWallet] = _tTotal.mul(7).div(100);
}
```

---

## 🛠️ Deployment & Setup

1. **Initialize Contract**
```solidity
constructor(
    0xTaxWallet, 
    0xDevWallet, 
    0xMarketingWallet,
    0xLiquidityWallet
)
```

2. **Configure DEX**
```bash
await token.setUniswapV2Router(ROUTER_ADDRESS);

await token.setUniswapV2Pair(token.address, WETH_ADDRESS);
```

3. **Launch Trading**
```solidity
await token.openTrading(); // Enables swaps
```

---

## ⚠️ Important Notes

- **Cooldown Period**: First 30 seconds after trading opens restrict transfers to exempt wallets
- **Fee Adjustment**: Only `_taxWallet` can reduce fees post-deployment
- **Ownership**: Recommend renouncing ownership after setup via `renounceOwnership()`

---

## 📄 License
MIT License - Full text included in SPDX header.

---

*Crafted with ❤️ by MUUIALabs. Audit recommended before mainnet deployment.*