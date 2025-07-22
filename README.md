

# sBTC-Thornecyst - Gaming Assets & Marketplace Smart Contract

This is a Clarity smart contract for managing **game asset NFTs**, an **on-chain marketplace**, and **player statistics**. It supports **batch minting**, **batch transfers**, **listings and sales**, and **stat tracking** — all within a secure, permissioned framework.

---

## 🔐 Ownership & Permissions

* **Contract Owner:** Set at deployment as `tx-sender`. Only the owner can mint new assets.
* **Users:** Any user can transfer assets (if transferable), purchase from marketplace, or update their own player stats.

---

## 📦 Features Overview

| Feature             | Description                                                                  |
| ------------------- | ---------------------------------------------------------------------------- |
| 🎨 Asset Minting    | Mint single or batch assets with metadata URI and transferability flag       |
| ♻️ Batch Operations | Efficiently mint or transfer multiple assets                                 |
| 🛒 Marketplace      | List assets for sale and purchase with STX                                   |
| 📈 Player Stats     | Track player level and experience                                            |
| 🔍 Read-only Views  | Retrieve asset details, marketplace listings, player stats, and total assets |

---

## 🧱 Data Structures

### 🔐 Constants

* `contract-owner`: Deploying account
* `max-level`: 100
* `max-experience`: 10,000
* `max-batch-size`: 10
* Error codes:

  * `err-owner-only` = `u100`
  * `err-not-found` = `u101`
  * `err-not-authorized` = `u102`
  * `err-invalid-input` = `u103`
  * `err-invalid-price` = `u104`

---

### 📘 Maps

```clojure
assets: { asset-id } => { owner, metadata-uri, transferable }

asset-prices: { asset-id } => { price }

marketplace-listings: { asset-id } => { seller, price, listed-at }

player-stats: { player } => { experience, level }
```

---

## 🚀 Public Functions

### 🧬 Minting

#### `mint-asset(metadata-uri, transferable)`

Mint a single asset with metadata URI and transferability flag.

* **Access:** Contract Owner only
* **Returns:** `asset-id`

---

#### `batch-mint-assets(metadata-uris, transferable-list)`

Mint up to 10 assets in one call.

* **Access:** Contract Owner only
* **Inputs:**

  * `metadata-uris`: List of strings (max length: 256)
  * `transferable-list`: List of booleans
* **Returns:** List of `asset-id`s

---

### 📤 Transfers

#### `transfer-asset(asset-id, recipient)`

Transfers an asset to another user.

* **Conditions:**

  * Sender must be the owner
  * Asset must be transferable
  * No self-transfers
* **Returns:** `true`

---

#### `batch-transfer-assets(asset-ids, recipients)`

Transfers multiple assets to recipients.

* Max 10 assets per call
* Matching length lists of IDs and recipients
* **Returns:** List of `true`s

---

### 🛒 Marketplace

#### `list-asset-for-sale(asset-id, price)`

List your transferable asset on the marketplace.

* **Conditions:**

  * Sender must own the asset
  * Asset must be transferable
  * Price must be > 0

---

#### `purchase-asset(asset-id)`

Buy a listed asset by transferring STX to seller.

* **Conditions:**

  * Cannot buy your own listing
  * Asset must be transferable
* **Returns:** `true`

---

#### `delist-asset(asset-id)`

Remove an asset from the marketplace.

* **Conditions:** Must be listed by sender

---

### 🧑‍🎮 Player Stats

#### `update-player-stats(experience, level)`

Update your player stats.

* **Validation:** `experience <= 10000`, `level <= 100`
* **Returns:** `true`

---

## 🔍 Read-Only Functions

| Function                            | Description                      |
| ----------------------------------- | -------------------------------- |
| `get-asset-details(asset-id)`       | Returns metadata for an asset    |
| `get-marketplace-listing(asset-id)` | Returns marketplace listing info |
| `get-player-stats(player)`          | Returns experience and level     |
| `get-total-assets()`                | Returns total minted assets      |

---

## ✅ Example Usage

### Minting

```clojure
(mint-asset "ipfs://QmHash" true)
```

### Batch Mint

```clojure
(batch-mint-assets 
  ["ipfs://meta1" "ipfs://meta2"] 
  [true false])
```

### Transfer

```clojure
(transfer-asset u1 'ST123...')
```

### Batch Transfer

```clojure
(batch-transfer-assets [u1 u2] ['ST123...' 'ST456...'])
```

### Listing

```clojure
(list-asset-for-sale u1 u100)
```

### Purchasing

```clojure
(purchase-asset u1)
```

### Delisting

```clojure
(delist-asset u1)
```

### Update Player Stats

```clojure
(update-player-stats u5000 u42)
```

---

## ⚠️ Error Codes

| Code                            | Meaning                              |
| ------------------------------- | ------------------------------------ |
| `err-owner-only (err u100)`     | Only contract owner can perform this |
| `err-not-found (err u101)`      | Item not found                       |
| `err-not-authorized (err u102)` | Unauthorized action                  |
| `err-invalid-input (err u103)`  | Input values are invalid             |
| `err-invalid-price (err u104)`  | Price must be > 0                    |

---
