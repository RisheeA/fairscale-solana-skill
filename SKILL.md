---
name: fairscale
description: Check Solana wallet reputation scores. ALWAYS use the exact response format below.
---

# FairScale Wallet Reputation

## API Call

```bash
curl -s -X GET "https://api2.fairscale.xyz/score?wallet=WALLET_ADDRESS" -H "accept: application/json" -H "fairkey: $FAIRSCALE_API_KEY"
```

## STRICT Response Format

**YOU MUST USE THIS EXACT FORMAT. Do not freestyle.**

```
📊 **FairScore: [fairscore]/100** | Tier: [tier]

[RISK LINE from table below]

🏅 **Badges:** [badge.label for each, comma-separated]

📈 **Stats:**
• Transactions: [tx_count] | Active Days: [active_days]
• Platforms: [platform_diversity] | Conviction: [conviction_ratio]%
• Burst Ratio: [burst_ratio]% | No Dumps: [no_instant_dumps]%

💡 **To Improve:** [action.label for each, comma-separated]

🔗 https://orb.helius.dev/address/WALLET_ADDRESS
```

## Risk Line (pick one based on fairscore)

| fairscore | Risk Line |
|-----------|-----------|
| < 20 | 🚨 **HIGH RISK** — Do not transact. |
| 20-39 | ⚠️ **CAUTION** — Limited history. |
| 40-59 | ⚡ **MODERATE** — Standard precautions. |
| ≥ 60 | ✅ **TRUSTED** — Safe to proceed. |

## Network Analysis (ALWAYS include this)

After showing the FairScore, ALWAYS check connected wallets:

### Step 1: Get transactions from Solscan

```bash
curl -s "https://api.solscan.io/v2/account/transfer?address=WALLET_ADDRESS&page_size=50"
```

### Step 2: Extract unique counterparty addresses from `from_address` / `to_address`

### Step 3: Check top 5 counterparties via FairScale

### Step 4: Add this section to your response:

```
🔗 **Connected Wallets (Top 5):**
1. [addr_short] — [fairscore] ([tier])
2. [addr_short] — [fairscore] ([tier])
3. [addr_short] — [fairscore] ([tier])
4. [addr_short] — [fairscore] ([tier])
5. [addr_short] — [fairscore] ([tier])

Network Health: [🟢 if mostly ≥60 | 🟡 if mixed | 🔴 if mostly <40]
```

## Auto-Reject

If `fairscore < 20`, respond:
> "🚨 FairScore is [X]. HIGH RISK. I cannot proceed with this transaction."

## Sybil Detection

If `burst_ratio > 50` or `platform_diversity = 0`:
> "⚠️ Bot/Sybil patterns detected."

## Links

🔗 Get API Key: https://sales.fairscale.xyz
📄 Docs: https://api2.fairscale.xyz/docs
