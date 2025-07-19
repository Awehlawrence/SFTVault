

# SFTVault -  Asset Smart Contract

A **Clarity smart contract** for managing tokenized assets with defined metadata, supply limits, and price history. This contract allows the creation, distribution, transfer, and authorization of **semi-fungible tokens** (SFTs) with an admin-controlled pricing model.

---

## 🚀 Features

* 🪙 **Token Minting** with custom name, category, max supply, and price.
* 💰 **Transfer & Authorization** between users.
* 🛡️ **Delegated Spending** via approved allowances.
* ⏳ **Price History Tracking** for each token over time.
* 🔐 **Admin Controls** for minting, price changes, and admin replacement.

---

## 🏗️ Data Structures

### 📁 `tokens`

Stores token metadata:

```clarity
{ token-id: uint } => {
  token-name: (string-ascii 64),
  token-category: (string-ascii 32),
  max-supply: uint,
  token-price: uint,
  last-price-update: uint
}
```

### 📊 `balances`

Holds user balances per token:

```clarity
{ holder: principal, token-id: uint } => { amount: uint }
```

### 🔐 `allowances`

Tracks delegated spend rights:

```clarity
{ holder: principal, authorized: principal, token-id: uint } => { allowed-amount: uint }
```

### 📈 `price-history`

Records price changes over time:

```clarity
{ token-id: uint, timestamp: uint } => { price: uint }
```

---

## 🧩 Public Functions

### ✅ `mint-token`

Creates a new token with:

* `token-name`
* `token-category`
* `max-supply`
* `token-price`

Only callable by the contract admin.

### ✅ `update-token-price`

Changes the token's price and logs the change in `price-history`.

### ✅ `authorize-spending`

Grants another principal permission to spend tokens on your behalf.

### ✅ `transfer`

Transfers tokens directly between users.

### ✅ `transfer-as-authorized`

Allows an authorized spender to move tokens from the owner's account.

### ✅ `set-contract-admin`

Reassigns the contract admin to a new principal.

---

## 🔍 Read-Only Functions

### 🔎 `get-token-details`

Returns metadata for a specific `token-id`.

### 🔎 `get-holder-balance`

Returns the amount of a token that a given principal holds.

### 🔎 `get-authorized-amount`

Returns how much a spender is allowed to transfer on behalf of a holder.

### 🔎 `get-price-at-time`

Returns a token's price at a specific block timestamp.

---

## 🚨 Errors

| Code   | Meaning                    |
| ------ | -------------------------- |
| `u100` | Not authorized             |
| `u101` | Token already exists       |
| `u102` | Token not found            |
| `u103` | Insufficient balance       |
| `u104` | Invalid token name         |
| `u105` | Invalid category           |
| `u106` | Invalid max supply         |
| `u107` | Invalid token price        |
| `u108` | Invalid recipient          |
| `u109` | Invalid transfer amount    |
| `u110` | Insufficient allowance     |
| `u111` | Invalid authorized address |
| `u112` | Invalid price update       |

---

## 🔐 Admin Role

The **contract admin** has exclusive access to:

* Minting new tokens
* Updating token prices
* Changing admin privileges

Initial admin is set to the deployer (`tx-sender` at deployment).

---

## 📦 Example Use Case

1. **Admin mints a token**:

```clarity
(mint-token "GoldToken" "Commodity" u100000 u50)
```

2. **Admin transfers to a user**:

```clarity
(transfer 'SP...user1 u1 u1000)
```

3. **User authorizes a DApp**:

```clarity
(authorize-spending 'SP...dapp u1 u500)
```

4. **DApp transfers on user's behalf**:

```clarity
(transfer-as-authorized 'SP...user1 'SP...merchant u1 u300)
```

5. **Admin updates the token price**:

```clarity
(update-token-price u1 u55)
```

---
