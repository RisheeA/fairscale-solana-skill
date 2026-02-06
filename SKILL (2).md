---
name: fairscale-solana
description: Check Solana wallet reputation scores before transactions. Returns FairScore (0-100), risk tier, badges, and connected wallet analysis.
license: MIT
metadata:
  author: FairScale
  version: "1.0.0"
  tags: ["solana", "wallet", "reputation", "sybil", "defi"]
---

# FairScale Wallet Reputation

Check any Solana wallet's reputation score before transacting.

## Quick Start

```bash
curl -s "https://api2.fairscale.xyz/score?wallet=WALLET_ADDRESS" \
  -H "accept: application/json" \
  -H "fairkey: $FAIRSCALE_API_KEY"
```

**Important:** The endpoint is `/score` — not `/api/v1/score` or anything else.

For full API details, see [references/API.md](references/API.md).

---

## Response Format

Use this EXACT format for every response:

```
📊 FairScore: [fairscore]/100 | Tier: [tier]

[RISK LINE]

🏅 Badges: [comma-separated badge labels]

📈 Stats: [tx_count] txns | [active_days] active days | [platform_diversity] platforms

💡 Improve: [comma-separated action labels]

🔗 Connected Wallets:
• [addr]... — [score] ([tier]) — https://orb.helius.dev/address/[full_addr]
• [addr]... — [score] ([tier]) — https://orb.helius.dev/address/[full_addr]
• [addr]... — [score] ([tier]) — https://orb.helius.dev/address/[full_addr]

Network: [🟢 Healthy | 🟡 Mixed | 🔴 Risky]
```

### Risk Lines

| Score | Line |
|-------|------|
| < 20 | 🚨 HIGH RISK — Do not transact |
| 20-39 | ⚠️ CAUTION — Limited history |
| 40-59 | ⚡ MODERATE — Standard precautions |
| ≥ 60 | ✅ TRUSTED — Safe to proceed |

---

## Connected Wallets (Required)

ALWAYS include connected wallet analysis. Steps:

1. Get recent transactions:
```bash
curl -s "https://api.mainnet-beta.solana.com" -X POST \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"getSignaturesForAddress","params":["WALLET_ADDRESS",{"limit":10}]}'
```

2. Get transaction details for each signature to find counterparty addresses

3. Check top 3-5 counterparties via FairScale API

4. Include in response with Orb links

---

## Auto-Reject

If `fairscore < 20`:

> "🚨 Cannot proceed. FairScore is [X] — HIGH RISK wallet."

## Sybil Detection

If `burst_ratio > 50` or `platform_diversity = 0`:

> "⚠️ Bot/Sybil patterns detected."

---

## Links

- Get API Key: https://sales.fairscale.xyz
- API Docs: https://api2.fairscale.xyz/docs
- Twitter: @FairScaleXYZ
