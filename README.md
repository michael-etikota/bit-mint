# 🪙 **BitMint Protocol**

*Turning Idle Bitcoin into Productive Capital*

---

## 📘 Overview

**BitMint** is a decentralized overcollateralized lending protocol built on the **Stacks blockchain**, designed to unlock the latent value of Bitcoin without sacrificing self-custody or upside exposure.

By locking BTC-backed assets as collateral, users can **mint synthetic stablecoins**, gain **instant liquidity**, and participate in **Bitcoin-native DeFi**—all while leveraging Stacks’ **Clarity smart contracts** for transparent and trust-minimized settlement.

---

## ⚙️ System Overview

BitMint enables three primary user actions:

1. **Deposit BTC collateral** – Lock Bitcoin or supported assets for protocol participation.
2. **Request a loan** – Mint synthetic stablecoins against overcollateralized BTC positions.
3. **Repay or get liquidated** – Repay loans with accrued interest or face liquidation if collateral ratio drops below threshold.

The system relies on a **price oracle** for BTC and other supported assets to determine collateral health.
Platform administrators can configure system parameters such as collateral ratios, liquidation thresholds, and price feeds.

---

## 🧩 Contract Architecture

### **Core Contract: `bitmint.clar`**

The smart contract serves as the central protocol logic, managing collateralized loans, user positions, and price feeds.

| Component                  | Type       | Description                                                                       |
| -------------------------- | ---------- | --------------------------------------------------------------------------------- |
| **Platform Configuration** | `data-var` | Stores key parameters like collateral ratio, liquidation threshold, and fee rate. |
| **Loan Registry**          | `map`      | Tracks each loan’s borrower, collateral, amount, rate, and status.                |
| **User Loans Index**       | `map`      | Maintains a per-user list of active loan IDs for quick lookup.                    |
| **Collateral Prices**      | `map`      | Holds price oracle data for supported assets.                                     |

---

## 🧠 Functional Overview

### 1. **Administrative Controls**

| Function                       | Description                                           |
| ------------------------------ | ----------------------------------------------------- |
| `initialize-platform`          | Enables the protocol after deployment.                |
| `update-collateral-ratio`      | Adjusts the minimum required collateral ratio.        |
| `update-liquidation-threshold` | Updates the threshold below which liquidation occurs. |
| `update-price-feed`            | Updates oracle-provided asset prices.                 |

All admin actions require authorization by the **contract owner**.

---

### 2. **User Operations**

| Function             | Description                                                     |
| -------------------- | --------------------------------------------------------------- |
| `deposit-collateral` | Locks BTC collateral into the protocol.                         |
| `request-loan`       | Opens a new loan position if sufficient collateral is provided. |
| `repay-loan`         | Repays loan principal + interest and unlocks collateral.        |

Collateral and loan values are dynamically validated against live oracle prices.

---

### 3. **Protocol Mechanics**

#### **Collateralization**

Loans must maintain a minimum collateral ratio (default `150%`).
If BTC price drops and the ratio falls below the **liquidation threshold** (default `120%`), the position is subject to **forced liquidation**.

#### **Interest Accrual**

Interest accrues block-by-block using a lightweight computation model based on the loan’s `interest-rate` and elapsed block height.

#### **Liquidation Logic**

When triggered, the protocol automatically:

* Marks the loan as `"liquidated"`,
* Removes it from the borrower’s active loans,
* Adjusts total locked collateral.

---

## 🏗️ (Optional) System Architecture

```
 ┌────────────────────────────────────────────┐
 │                User Layer                  │
 │────────────────────────────────────────────│
 │  • Deposit Collateral                      │
 │  • Request Loan                            │
 │  • Repay / Withdraw                        │
 └────────────────────────────────────────────┘
                 │
                 ▼
 ┌────────────────────────────────────────────┐
 │          BitMint Smart Contract            │
 │────────────────────────────────────────────│
 │  • Loan Registry (loans map)               │
 │  • User Loan Index (user-loans map)        │
 │  • Collateral Prices (oracle map)          │
 │  • Core Logic: Validation, Interest,       │
 │    Liquidation, Fee Handling               │
 └────────────────────────────────────────────┘
                 │
                 ▼
 ┌────────────────────────────────────────────┐
 │            Oracle & Admin Layer            │
 │────────────────────────────────────────────│
 │  • BTC / STX Price Feeds                   │
 │  • Collateral Ratio / Threshold Updates    │
 │  • Governance Controls                     │
 └────────────────────────────────────────────┘
```

---

## 🔐 Security & Design Principles

* **Trust-minimized Bitcoin DeFi:** Utilizes Stacks’ Bitcoin finality for secured state anchoring.
* **Full Transparency:** All operations—interest, liquidation, and price updates—are on-chain auditable.
* **Overcollateralization:** Protects the system from undercollateralized loans and default risk.
* **Upgradeable Parameters:** Admins can tune risk parameters while maintaining immutability of core logic.

---

## 🧾 Data Flow Summary

| Step | Action               | On-Chain State Change                                            |
| ---- | -------------------- | ---------------------------------------------------------------- |
| 1    | `deposit-collateral` | Increments `total-btc-locked`                                    |
| 2    | `request-loan`       | Creates a new record in `loans` and updates `user-loans`         |
| 3    | `update-price-feed`  | Updates oracle price used in ratio checks                        |
| 4    | `repay-loan`         | Marks loan as `repaid` and reduces `total-btc-locked`            |
| 5    | `check-liquidation`  | Compares live ratio → triggers `liquidate-position` if necessary |

---

## 🧱 Future Enhancements

* Integration with **decentralized oracles** (e.g., Chainlink, ALEX Oracle).
* Support for **multi-asset collateral baskets** beyond BTC/STX.
* Dynamic interest rate models.
* Automated liquidation auctions.

---

## 🪙 Summary

BitMint bridges Bitcoin’s passive capital with decentralized finance’s active liquidity — **without giving up custody or exposure**.
Through transparent Clarity contracts and robust collateralization logic, the protocol sets a new standard for **Bitcoin-backed lending** on Stacks.
