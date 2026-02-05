---
name: fairscale
description: Query Solana wallet reputation scores via FairScale API. Get FairScores, badges, feature breakdowns, and tier classifications for Sybil detection and creditworthiness assessment.
---

# FairScale - Solana Wallet Reputation Scoring

FairScale provides real-time reputation scoring for Solana wallets using a dynamic neural network that retrains daily.

## What Users Can Ask You

Once this skill is configured, users can message you naturally:

- "What's the FairScore for 7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU?"
- "Check the reputation of this wallet: [address]"
- "Is this wallet trustworthy for a trade?"
- "Does this wallet look like a Sybil or bot?"
- "Show me the full breakdown for this address"
- "What badges does this wallet have?"
- "Should I trust this wallet for a large transaction?"

## How to Query the API

**Base URL:** `https://api2.fairscale.xyz`

**Authentication:** Include the `fairkey` header with every request:
```
fairkey: $FAIRSCALE_API_KEY
```

### GET /score - Complete Score (Use This Most Often)

Returns full FairScore with metadata, badges, features, and tier.

```bash
curl -X GET \
  'https://api2.fairscale.xyz/score?wallet=<WALLET_ADDRESS>' \
  -H 'accept: application/json' \
  -H "fairkey: $FAIRSCALE_API_KEY"
```

**Example Response:**
```json
{
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "fairScore": 78,
  "walletScore": 82,
  "tier": "trusted",
  "badges": [
    {"id": "diamond_hands", "label": "Diamond Hands", "description": "Long-term holder", "tier": "gold"}
  ],
  "features": {
    "tx_count": 1247,
    "active_days": 312,
    "wallet_age_days": 547,
    "platform_diversity": 12,
    "conviction_ratio": 0.85,
    "burst_ratio": 0.12,
    "no_instant_dumps": true
  }
}
```

### GET /fairScore - Score Only

Lightweight endpoint returning just the FairScore number. Use for quick checks.

```bash
curl -X GET \
  'https://api2.fairscale.xyz/fairScore?wallet=<WALLET_ADDRESS>' \
  -H 'accept: application/json' \
  -H "fairkey: $FAIRSCALE_API_KEY"
```

### GET /walletScore - Wallet Score Only

Returns the wallet-specific behavioral score.

```bash
curl -X GET \
  'https://api2.fairscale.xyz/walletScore?wallet=<WALLET_ADDRESS>' \
  -H 'accept: application/json' \
  -H "fairkey: $FAIRSCALE_API_KEY"
```

## How to Interpret Results

### FairScore Tiers
- **80+**: Highly trusted, established wallet
- **60-79**: Good standing, regular activity
- **40-59**: Limited history, proceed with caution
- **20-39**: Some risk signals detected
- **<20**: Multiple red flags, high risk

### Key Features to Check

| Feature | What It Means | Trustworthy | Suspicious |
|---------|---------------|-------------|------------|
| `wallet_age_days` | How old the wallet is | >180 days | <30 days |
| `active_days` | Days with transactions | >50 | <10 |
| `platform_diversity` | Different protocols used | >5 | 1-2 |
| `conviction_ratio` | Holds tokens long-term | High (>0.5) | Low (<0.2) |
| `burst_ratio` | Burst activity patterns | Low (<0.3) | High (>0.7) = bot-like |
| `no_instant_dumps` | Doesn't dump immediately | true | false |
| `tx_count` | Total transactions | High | Very low |

### Sybil/Bot Detection
When checking if a wallet might be a Sybil or bot, look for:
- High `burst_ratio` (activity in suspicious bursts)
- Low `platform_diversity` (only uses 1-2 protocols)
- Very new `wallet_age_days` with high `tx_count` (new wallet, lots of activity)
- `no_instant_dumps` = false (dumps tokens immediately after receiving)

## Example Responses to Users

**User asks:** "Is 7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU trustworthy?"

**You should:** Query `/score`, then respond with something like:
> "This wallet has a FairScore of 78 (trusted tier). It's 547 days old with 1,247 transactions across 12 different platforms. The conviction ratio is high (0.85) meaning they hold tokens long-term rather than flipping. Low burst ratio (0.12) suggests organic activity, not bot behavior. They've earned a 'Diamond Hands' badge. Overall, this looks like a legitimate, established wallet."

**User asks:** "Does this look like a Sybil?"

**You should:** Query `/score`, check the features, then respond:
> "Checking for Sybil indicators... The burst ratio is 0.12 (low, organic pattern), platform diversity is 12 (uses many protocols), and wallet age is 547 days. No instant dump behavior detected. This doesn't match typical Sybil patterns — looks like a real user."

## Error Handling

| Code | Meaning | What to Tell User |
|------|---------|-------------------|
| 200 | Success | Return the data |
| 401 | Invalid API key | "API key issue — the agent owner needs to check the FairScale configuration" |
| 404 | Wallet not found | "No reputation data found for this wallet — it may be too new or inactive" |
| 429 | Rate limited | "Rate limit reached — try again in a moment" |

## Links

- **Website:** https://fairscale.xyz
- **Get API Key:** https://sales.fairscale.xyz
- **API Docs:** https://api2.fairscale.xyz/docs
- **$FAIR Token:** https://fairscale.xyz/token
- **Twitter:** [@FairScaleXYZ](https://twitter.com/FairScaleXYZ)
