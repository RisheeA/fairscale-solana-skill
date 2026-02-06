---
name: fairscale
description: Check Solana wallet reputation. ALWAYS check connected wallets too.
---

# FairScale API

## IMPORTANT: Correct API Call

The ONLY endpoint is:

```
GET https://api2.fairscale.xyz/score?wallet=WALLET_ADDRESS
```

Headers:
```
accept: application/json
fairkey: $FAIRSCALE_API_KEY
```

Example:
```bash
curl -s "https://api2.fairscale.xyz/score?wallet=5G5HDvbib4CyHxVgm4RHiVY5RfbDuFfp6BiH5xgZXczT" -H "accept: application/json" -H "fairkey: $FAIRSCALE_API_KEY"
```

DO NOT use any other endpoint. There is no `/api/v1/` path.

---

## REQUIRED: Two-Part Response

Every FairScale check MUST have TWO parts:

### PART 1: Wallet Score

```
📊 FairScore: [fairscore]/100 | Tier: [tier]

[Risk level - see table below]

🏅 Badges: [comma-separated badge labels]

📈 Stats: [tx_count] txns | [active_days] active days | [platform_diversity] platforms

💡 Improve: [comma-separated action labels]
```

Risk levels:
- fairscore < 20 → "🚨 HIGH RISK — Do not transact"
- fairscore 20-39 → "⚠️ CAUTION — Limited history"  
- fairscore 40-59 → "⚡ MODERATE — Standard precautions"
- fairscore ≥ 60 → "✅ TRUSTED — Safe to proceed"

### PART 2: Connected Wallets (MANDATORY)

You MUST always include this. Get recent transactions, extract counterparty addresses, check their FairScores.

Step 1 - Get transactions:
```bash
curl -s "https://api.solscan.io/v2/account/transfer?address=WALLET_ADDRESS&page_size=20"
```

Step 2 - Extract unique addresses from `from_address` and `to_address` fields

Step 3 - Check top 3-5 via FairScale API

Step 4 - Add to response:

```
🔗 Connected Wallets:
• [short_addr] — Score: [X] ([tier]) — https://orb.helius.dev/address/[full_addr]
• [short_addr] — Score: [X] ([tier]) — https://orb.helius.dev/address/[full_addr]
• [short_addr] — Score: [X] ([tier]) — https://orb.helius.dev/address/[full_addr]

Network: [🟢 Healthy / 🟡 Mixed / 🔴 Risky]
```

---

## Complete Example Response

```
📊 FairScore: 43/100 | Tier: Silver

⚡ MODERATE — Standard precautions

🏅 Badges: LST Staker, Diamond Hands, Veteran, Active Trader

📈 Stats: 156 txns | 89 active days | 12 platforms

💡 Improve: Hold Longer, Diversify More

🔗 Connected Wallets:
• 4mtV...txot — Score: 62 (Silver) — https://orb.helius.dev/address/4mtV...
• 8u7v...ua3E — Score: 78 (Gold) — https://orb.helius.dev/address/8u7v...
• 9xKz...ab2F — Score: 15 (Bronze) — https://orb.helius.dev/address/9xKz...

Network: 🟡 Mixed
```

---

## Auto-Reject

If fairscore < 20:
> "🚨 Cannot proceed. FairScore is [X] — HIGH RISK wallet."

## Links

Get API Key: https://sales.fairscale.xyz
