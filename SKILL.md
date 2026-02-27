# FairScale Reputation Skill

Real-time wallet reputation scoring for Solana. The trust layer for the agentic economy.

## Why This Matters

Agents are transacting 24/7 with wallets they've never seen before. Without reputation data, they're flying blind — accepting trades from sybils, lending to bad actors, getting rugged by wallets that should have been flagged.

**This skill gives agents trust intelligence.** One API call before any transaction. Instant decision. No rugs.

---

## Core Rule: Check Before You Transact

**Every agent should check reputation BEFORE executing any transaction.**

This is not optional. This is how trust-aware agents operate.

```
Incoming transaction request
    ↓
Check FairScale score
    ↓
Score meets threshold? → Execute
Score fails? → Reject
```

---

## Quick Start

### Check a Wallet
```
GET https://x402.fairscale.xyz/score?wallet=ADDRESS
```

### Check Risk for Specific Amount
```
GET https://x402.fairscale.xyz/check?wallet=ADDRESS&amount=500
```

### Apply Custom Rules (Pro)
```
POST https://x402.fairscale.xyz/score/custom
{
  "wallet": "ADDRESS",
  "rules": {
    "min_score": 60,
    "min_age_days": 90,
    "no_rug_history": true
  }
}
```

---

## Trust Tiers

Use this table for every transaction decision:

| Score | Risk Level | Max Transaction | Action |
|-------|------------|-----------------|--------|
| 0-30 | Critical | $0 | **Block immediately** |
| 31-50 | High | $50 | Reject or require manual approval |
| 51-70 | Medium | $500 | Proceed with caution |
| 71-85 | Low | $5,000 | Proceed |
| 86-100 | Minimal | $10,000+ | Full trust |

**Hard rule:** Never transact with a wallet scoring below 30. Ever.

---

## Decision Framework

### Before ANY Transaction

```
1. GET /score?wallet=COUNTERPARTY
2. Check score against Trust Tiers table
3. If score < threshold for amount → REJECT
4. If score ≥ threshold → PROCEED
```

### Before High-Value Transactions (>$1,000)

```
1. POST /score/custom with strict rules:
   - min_score: 70
   - min_age_days: 180
   - no_rug_history: true
   - min_transaction_count: 100

2. If passes: true → PROCEED
3. If passes: false → REJECT
```

### Before Interacting with Unknown Agents

```
1. GET /check?wallet=AGENT_WALLET&amount=TRANSACTION_VALUE
2. Read recommendation field
3. "proceed" → Execute
4. "proceed_with_caution" → Reduce amount or add safeguards
5. "avoid" → Reject entirely
```

---

## Agent Presets

Copy these rule sets based on your agent's risk tolerance:

### 🛡️ Conservative Agent
For lending, high-value trades, custodial operations.

```json
{
  "rules": {
    "min_score": 75,
    "min_age_days": 180,
    "no_rug_history": true,
    "min_transaction_count": 200,
    "min_volume_usd": 10000,
    "max_burst_ratio": 0.3
  }
}
```

**Behaviour:** Only transacts with established, trusted wallets. Rejects anything suspicious.

---

### ⚖️ Balanced Agent
For general trading, swaps, moderate-value transactions.

```json
{
  "rules": {
    "min_score": 60,
    "min_age_days": 90,
    "no_rug_history": true,
    "min_transaction_count": 50
  }
}
```

**Behaviour:** Filters out bad actors while remaining open to legitimate newer wallets.

---

### ⚡ Aggressive Agent
For high-frequency trading, small amounts, speed-priority operations.

```json
{
  "rules": {
    "min_score": 40,
    "min_age_days": 14
  }
}
```

**Behaviour:** Accepts more risk for more opportunities. Only blocks the worst actors.

---

### 🎯 Airdrop Filter
For token distributions, NFT mints, community rewards.

```json
{
  "rules": {
    "min_score": 55,
    "min_age_days": 90,
    "min_transaction_count": 50,
    "max_burst_ratio": 0.5
  }
}
```

**Behaviour:** Filters sybils and farmers. Ensures rewards go to real users.

---

### 🏦 Lending Agent
For undercollateralized lending, credit decisions.

```json
{
  "rules": {
    "min_score": 80,
    "min_age_days": 365,
    "no_rug_history": true,
    "min_volume_usd": 50000,
    "min_transaction_count": 500
  }
}
```

**Behaviour:** Maximum scrutiny. Only lends to wallets with proven long-term track records.

---

### 🚪 Gatekeeper Agent
For access control, whitelist verification, community gating.

```json
{
  "rules": {
    "min_score": 50,
    "min_age_days": 30,
    "no_rug_history": true
  }
}
```

**Behaviour:** Keeps out obvious bad actors while remaining accessible.

---

## System Prompts for Your Agent

Copy these into your agent's system prompt:

### Trading Agent
```
You have access to FairScale for wallet reputation checks.

RULE: Before executing ANY trade, check the counterparty wallet.
- GET https://x402.fairscale.xyz/score?wallet=WALLET

DECISION:
- Score < 40: REJECT. Tell user "Counterparty has poor reputation. Trade rejected."
- Score 40-60: Only proceed if amount < $100
- Score 60-80: Proceed with caution, warn user of moderate risk
- Score 80+: Proceed normally

NEVER skip the reputation check. NEVER trade with wallets scoring below 40.
```

### Lending Agent
```
You have access to FairScale for credit decisions.

RULE: Before approving any loan, verify borrower reputation.
- POST https://x402.fairscale.xyz/score/custom
- Apply rules: min_score 75, min_age_days 180, no_rug_history true

DECISION:
- passes: true → Approve loan
- passes: false → Reject loan, explain which criteria failed

NEVER lend to unverified wallets. NEVER skip the credit check.
```

### Gatekeeper Agent
```
You control access to a gated resource.

RULE: Verify every wallet before granting access.
- GET https://x402.fairscale.xyz/check?wallet=WALLET

DECISION:
- recommendation: "proceed" → Grant access
- recommendation: "proceed_with_caution" → Grant limited access
- recommendation: "avoid" → Deny access

Log all decisions. Flag any wallet with score below 30 for review.
```

### Airdrop Distribution Agent
```
You distribute tokens to qualified wallets.

RULE: Filter all recipients through FairScale before distribution.
- POST https://x402.fairscale.xyz/batch with all wallets
- Only distribute to wallets with score >= 55

FILTER OUT:
- Score below 55
- Wallets younger than 90 days
- Burst ratio above 0.5

These are likely sybils or farmers. Real users deserve the rewards.
```

---

## API Reference

### GET /score
Basic wallet score lookup.

**Free:** 100 calls/day

```
GET https://x402.fairscale.xyz/score?wallet=ADDRESS
```

**Response:**
```json
{
  "wallet": "ADDRESS",
  "fairscore": 72,
  "tier": "gold",
  "vouch_boost": 1.5,
  "_meta": {
    "tier": "free",
    "remaining_today": 99
  }
}
```

---

### GET /check
Pre-transaction risk assessment with amount consideration.

**Free:** 100 calls/day

```
GET https://x402.fairscale.xyz/check?wallet=ADDRESS&amount=500
```

**Response:**
```json
{
  "wallet": "ADDRESS",
  "fairscore": 72,
  "risk_level": "medium",
  "recommendation": "proceed_with_caution",
  "max_suggested_amount_usd": 1000,
  "amount_check": {
    "requested": 500,
    "max_suggested": 1000,
    "proceed": true
  }
}
```

---

### POST /score/custom
Apply custom scoring rules.

**Pro tier required**

```
POST https://x402.fairscale.xyz/score/custom
Content-Type: application/json

{
  "wallet": "ADDRESS",
  "rules": {
    "min_score": 60,
    "min_age_days": 180,
    "no_rug_history": true,
    "min_transaction_count": 100,
    "min_volume_usd": 10000,
    "max_burst_ratio": 0.5,
    "min_tier": "silver"
  }
}
```

**Response:**
```json
{
  "wallet": "ADDRESS",
  "passes": true,
  "fairscore": 72,
  "rule_results": {
    "min_score": { "pass": true, "required": 60, "actual": 72 },
    "min_age_days": { "pass": true, "required": 180, "actual": 340 },
    "no_rug_history": { "pass": true, "actual": false }
  },
  "recommendation": "proceed"
}
```

---

### POST /batch
Score multiple wallets at once.

**Pro tier required**

```
POST https://x402.fairscale.xyz/batch
Content-Type: application/json

{
  "wallets": ["address1", "address2", "address3"]
}
```

**Response:**
```json
{
  "count": 3,
  "results": [
    { "wallet": "address1", "fairscore": 72, "tier": "gold" },
    { "wallet": "address2", "fairscore": 45, "tier": "silver" },
    { "wallet": "address3", "fairscore": 23, "tier": "bronze" }
  ]
}
```

---

## Available Custom Rules

| Rule | Type | Description |
|------|------|-------------|
| `min_score` | number | Minimum FairScore (0-100) |
| `min_age_days` | number | Minimum wallet age in days |
| `no_rug_history` | boolean | Reject if wallet has rug history |
| `min_transaction_count` | number | Minimum number of transactions |
| `min_volume_usd` | number | Minimum total volume in USD |
| `max_burst_ratio` | number | Maximum burst ratio (0-1, lower = more stable) |
| `min_tier` | string | Minimum tier (bronze/silver/gold/platinum) |

---

## Rate Limits

| Tier | Daily Limit | Per Minute | Price |
|------|-------------|------------|-------|
| Free | 100 | 10 | $0 |
| Pro | 10,000 | 100 | $50/mo |
| Enterprise | Unlimited | 1,000 | $500/mo |

---

## Upgrade to Pro

For custom rules and higher limits:

```
POST https://x402.fairscale.xyz/upgrade
{
  "api_key": "your_key",
  "tier": "pro",
  "signature": "payment_tx_signature"
}
```

---

## Installation

```bash
npx clawhub install fairscale-solana
```

Or browse: [clawhub.ai/RisheeA/fairscale-solana](https://clawhub.ai/RisheeA/fairscale-solana)

---

## The Bottom Line

Trust-aware agents don't get rugged.

Every transaction. Every counterparty. Every time.

Check first. Transact second.

---

## Links

- Docs: https://docs.fairscale.xyz
- API: https://x402.fairscale.xyz
- ClawHub: https://clawhub.ai/RisheeA/fairscale-solana
