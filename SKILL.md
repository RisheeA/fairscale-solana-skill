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
  "wallet": "BYkSj6ekvJSLq1j4CrDeuT64NUT2NZ1xUau8uiGomcvF",
  "fairscore_base": 18.9,
  "social_score": 0.0,
  "fairscore": 18.9,
  "tier": "bronze",
  "badges": [
    {"id": "lst_staker", "label": "LST Staker", "description": "Holds Liquid Staking Tokens", "tier": "gold"},
    {"id": "no_dumper", "label": "No Instant Dumps", "description": "Never panic sells", "tier": "silver"}
  ],
  "actions": [
    {"id": "increase_lst", "label": "Increase LST Holdings", "description": "Top scorers hold 50%+ of portfolio in LST", "priority": "medium", "cta": "Stake More →"},
    {"id": "hold_longer", "label": "Hold Positions Longer", "description": "Diamond hands hold for 30+ days", "priority": "medium", "cta": "Learn More →"},
    {"id": "diversify_platforms", "label": "Try More Platforms", "description": "Explore Jupiter, Raydium, Orca, and more", "priority": "low", "cta": "Discover DEXes →"}
  ],
  "timestamp": "2026-02-06T16:55:49.392038Z",
  "features": {
    "lst_percentile_score": 45.0,
    "major_percentile_score": 32.5,
    "native_sol_percentile": 17.47,
    "stable_percentile_score": 94.01,
    "tx_count": 0,
    "active_days": 0,
    "median_gap_hours": 24,
    "tempo_cv": 0,
    "burst_ratio": 0,
    "net_sol_flow_30d": 0,
    "median_hold_days": 0,
    "no_instant_dumps": 1,
    "conviction_ratio": 0,
    "platform_diversity": 0,
    "wallet_age_score": 0
  }
}
```

**Key fields:**
- `fairscore` — The main reputation score (0-100)
- `tier` — bronze, silver, gold, etc.
- `badges` — Achievements earned by the wallet
- `actions` — Recommended actions to improve score
- `features` — Detailed scoring breakdown

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

### Tiers (from `tier` field)
- **diamond**: Elite wallet, highest trust
- **gold**: Excellent reputation
- **silver**: Good standing
- **bronze**: Limited history or new wallet
- **unranked**: Insufficient data

### FairScore Ranges (from `fairscore` field)
- **80+**: Highly trusted, established wallet
- **60-79**: Good standing, regular activity
- **40-59**: Moderate history
- **20-39**: Limited history, proceed with caution
- **<20**: High risk, multiple red flags

### Key Features to Check (from `features` object)

| Feature | What It Means | Trustworthy | Suspicious |
|---------|---------------|-------------|------------|
| `wallet_age_score` | How established the wallet is | High | 0 or very low |
| `active_days` | Days with transactions | >50 | 0 |
| `tx_count` | Total transactions | High | 0 |
| `platform_diversity` | Different protocols used | >5 | 0-1 |
| `conviction_ratio` | Holds tokens long-term | High (>0.5) | 0 |
| `burst_ratio` | Burst activity patterns | 0 or low | High = bot-like |
| `no_instant_dumps` | Doesn't dump immediately | 1 (true) | 0 (false) |
| `median_hold_days` | How long tokens are held | >30 | 0 |
| `lst_percentile_score` | Liquid staking rank | High | Low |
| `native_sol_percentile` | SOL holdings rank | High | Low |

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

## Agent-to-Agent Reputation Gating

When interacting with other agents or wallets, automatically check their reputation before proceeding.

### Automatic Scoring Rule

**Before accepting any transaction, trade, or interaction from another wallet/agent:**

1. Extract their Solana wallet address
2. Query their FairScore using the `/score` endpoint
3. Read the `fairscore` field from the response
4. Apply this policy:

| fairscore | Action |
|-----------|--------|
| **< 20** | **REJECT** — High risk, do not proceed. Inform the user: "This wallet has a FairScore of {fairscore} (high risk). Transaction declined for safety." |
| **20-39** | **WARN** — Caution advised. Inform the user: "This wallet has a FairScore of {fairscore}. Proceed with caution?" |
| **40-59** | **ALLOW WITH NOTE** — Limited history. Proceed normally but mention: "This wallet has a FairScore of {fairscore} with limited reputation history." |
| **≥ 60** | **ALLOW** — Trusted. Proceed normally. |

### Example: Checking a Counterparty Before Trade

When someone wants to trade with you or send you tokens:

```bash
curl -X GET \
  'https://api2.fairscale.xyz/score?wallet=<THEIR_WALLET>' \
  -H 'accept: application/json' \
  -H "fairkey: $FAIRSCALE_API_KEY"
```

Parse the response and check the `fairscore` field:

**If fairscore < 20:**
> "⚠️ I've checked this wallet's reputation. FairScore: {fairscore}, Tier: {tier}. This is HIGH RISK. I cannot proceed with this transaction."

**If fairscore 20-39:**
> "⚠️ FairScore: {fairscore}, Tier: {tier}. This wallet has limited reputation. Do you want to proceed anyway?"

**If fairscore ≥ 60:**
> "✅ FairScore: {fairscore}, Tier: {tier}. Reputation check passed. Badges: {badges}."

### Include Badges and Actions in Response

The API also returns `badges` (achievements) and `actions` (recommendations). Include these for context:

> "This wallet has earned: LST Staker (gold), No Instant Dumps (silver). Recommended actions to improve: Hold Positions Longer, Try More Platforms."

### Sybil Detection for Agent Networks

When an agent wants to join your network, check the `features` object for bot patterns:

- `burst_ratio` > 0.5 = likely bot
- `platform_diversity` = 0 = suspicious
- `active_days` = 0 with holdings = dormant/suspicious
- `tx_count` = 0 = no transaction history

If Sybil indicators detected, reject with:
> "This wallet shows Sybil/bot patterns (burst_ratio: {burst_ratio}, platform_diversity: {platform_diversity}). Cannot join the network."

## Links

- **Website:** https://fairscale.xyz
- **Get API Key:** https://sales.fairscale.xyz
- **API Docs:** https://api2.fairscale.xyz/docs
- **$FAIR Token:** https://fairscale.xyz/token
- **Twitter:** [@FairScaleXYZ](https://twitter.com/FairScaleXYZ)
