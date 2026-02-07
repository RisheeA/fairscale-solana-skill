---
name: fairscale-solana
description: Check Solana wallet reputation. MUST call the API for every wallet — never guess or reuse previous data.
license: MIT
metadata:
  author: FairScale
  version: "2.1.0"
---

# FairScale Wallet Reputation

**Full API Docs:** https://docs.fairscale.xyz

## CRITICAL RULES

1. **ALWAYS call the API** — Never guess, estimate, or reuse data from previous wallets
2. **Each wallet is unique** — Do not assume similarity to other wallets
3. **Use ONLY the values from the API response** — Do not calculate or modify scores
4. **If the API fails, say so** — Do not make up data

---

## API ENDPOINT

```
GET https://api2.fairscale.xyz/score?wallet=WALLET_ADDRESS
```

**Headers:**
- `fairkey: YOUR_API_KEY` (required)

**Example Request:**
```bash
curl -X GET "https://api2.fairscale.xyz/score?wallet=7xKXtg2CM87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU" \
  -H "fairkey: YOUR_API_KEY"
```

---

## RESPONSE FIELDS

| Field | Type | Description |
|-------|------|-------------|
| `fairscore` | number | Combined FairScore (wallet + social) — **USE THIS** |
| `tier` | string | `bronze`, `silver`, `gold`, or `platinum` |
| `badges` | array | Earned badges with `label` and `description` |
| `actions` | array | Suggested improvements |

**DO NOT USE:** `fairscore_base`, `social_score`, or `features.*` in output.

---

## OUTPUT FORMAT

```
📊 FairScore: [fairscore]/100 | Tier: [tier]

[RISK LINE]

🏅 Badges: [badge labels, comma-separated]

💡 Improve: [action labels, or "None"]
```

**Risk line rules:**
- fairscore < 20 → `🚨 HIGH RISK — Do not transact`
- fairscore 20-39 → `⚠️ CAUTION — Limited history`
- fairscore 40-59 → `⚡ MODERATE — Standard precautions`
- fairscore ≥ 60 → `✅ TRUSTED — Safe to proceed`

---

## EXAMPLE

**API Response:**
```json
{
  "fairscore": 65.3,
  "tier": "gold",
  "badges": [
    {"label": "Diamond Hands", "description": "Long-term holder with conviction"}
  ],
  "actions": []
}
```

**Output:**
```
📊 FairScore: 65.3/100 | Tier: gold

✅ TRUSTED — Safe to proceed

🏅 Badges: Diamond Hands

💡 Improve: None
```

---

## ERRORS

If API returns error or no data:
```
❌ Could not retrieve FairScore for this wallet. Please try again.
```

**Never invent scores. Never guess. Always call the API.**

---

## GET API KEY

https://sales.fairscale.xyz
