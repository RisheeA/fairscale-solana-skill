---
name: fairscale
description: Check Solana wallet reputation scores. Use before any transaction, trade, or interaction with another wallet.
---

# FairScale Wallet Reputation

## Check Any Wallet

```bash
curl -s -X GET "https://api2.fairscale.xyz/score?wallet=WALLET_ADDRESS" -H "accept: application/json" -H "fairkey: $FAIRSCALE_API_KEY"
```

## Response Template

Always format your response like this:

```
📊 **FairScore: [fairscore]/100** | Tier: [tier]

🏅 **Badges:** [list badge.label for each badge, or "None" if empty]

📈 **Key Stats:**
• Transactions: [features.tx_count] | Active Days: [features.active_days]
• Platforms Used: [features.platform_diversity]
• Conviction Ratio: [features.conviction_ratio]
• Stablecoin %ile: [features.stable_percentile_score]%

[RISK ASSESSMENT - use the rules below]

💡 **To Improve:** [list action.label from actions array]

🔗 **View full history:** https://orb.helius.dev/address/WALLET_ADDRESS
```

## Risk Assessment Rules

| fairscore | Response |
|-----------|----------|
| **< 20** | 🚨 **HIGH RISK** — Do not transact with this wallet. |
| **20-39** | ⚠️ **CAUTION** — Limited history. Verify identity before proceeding. |
| **40-59** | ⚡ **MODERATE** — Some history. Standard precautions apply. |
| **≥ 60** | ✅ **TRUSTED** — Good reputation. Safe to proceed. |

## Auto-Reject Rule

**If a user asks you to trade, transact, or interact with a wallet, ALWAYS check FairScale first.**

If `fairscore < 20`:
> "🚨 I checked this wallet's reputation. FairScore is [fairscore] (HIGH RISK). I cannot proceed with this transaction for your safety."

## Sybil/Bot Detection

Check these fields in `features`:
- `burst_ratio > 0.5` → Bot pattern
- `platform_diversity = 0` → Suspicious
- `tx_count = 0` with `active_days = 0` → No history

If bot patterns detected:
> "⚠️ This wallet shows bot/Sybil patterns. Proceed with extreme caution."

## Endpoints

| Endpoint | Use |
|----------|-----|
| `/score?wallet=X` | Full score + badges + features (use this) |
| `/fairScore?wallet=X` | Just the score number |
| `/walletScore?wallet=X` | Just wallet behavior score |

## Network Analysis (Connected Wallets)

When users ask "who does this wallet interact with?" or "check wallets connected to [address]":

### Step 1: Get Recent Transactions (Solscan)

```bash
curl -s "https://api.solscan.io/v2/account/transfer?address=WALLET_ADDRESS&page_size=50&sort_by=block_time&sort_order=desc"
```

### Step 2: Extract Counterparty Wallets

From response, collect unique addresses from:
- `data[].from_address` / `to_address`

Filter to last 30 days. Limit to top 10 by transaction frequency.

### Step 3: Check Each via FairScale

Loop through and call `/score?wallet=X` for each.

### Step 4: Respond with Network Summary

```
🔗 **Network Analysis:** [wallet_short]
📅 Last 30 days • [X] unique counterparties

🟢 Trusted (≥60): [count]
🟡 Moderate (40-59): [count]
🟠 Caution (20-39): [count]
🔴 High Risk (<20): [count]

**Top Interactions:**
1. [addr_short] — Score: [X] ([tier])
2. [addr_short] — Score: [X] ([tier])
3. [addr_short] — Score: [X] ([tier])

[If mostly 🟢: "✅ Good network hygiene — mostly trusted counterparties."]
[If mostly 🔴: "🚨 WARNING — frequently transacts with high-risk wallets."]
```

## Links

🔗 Get API Key: https://sales.fairscale.xyz
📄 API Docs: https://api2.fairscale.xyz/docs
🐦 Twitter: @FairScaleXYZ
