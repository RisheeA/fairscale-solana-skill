# FairScale API Reference

## Authentication

All requests require Bearer token authentication:

```
Authorization: Bearer <your_api_key>
```

## Base URL

```
https://api.fairscale.xyz/v1
```

## Endpoints

### GET /score/{wallet_address}

Get reputation score for a Solana wallet.

**Parameters:**
- `wallet_address` (path, required): Base58-encoded Solana wallet address
- `include_signals` (query, optional): Include scoring breakdown (default: true)
- `include_history` (query, optional): Include score history (default: false)

**Example:**
```bash
curl -s -H "Authorization: Bearer $FAIRSCALE_API_KEY" \
  "https://api.fairscale.xyz/v1/score/7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU" | jq
```

**Response:**
```json
{
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "score": 78,
  "tier": "trusted",
  "signals": {
    "on_chain_activity": 85,
    "transaction_history": 72,
    "token_holdings": 80,
    "protocol_interactions": 75,
    "social_presence": 68
  },
  "flags": [],
  "computed_at": "2026-02-05T14:30:00Z"
}
```

---

### POST /score/batch

Score multiple wallets (max 100 per request).

**Request Body:**
```json
{
  "wallets": ["wallet1", "wallet2", ...],
  "min_score": 0,
  "include_signals": true
}
```

**Example:**
```bash
curl -s -X POST \
  -H "Authorization: Bearer $FAIRSCALE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"wallets": ["addr1", "addr2"], "min_score": 40}' \
  "https://api.fairscale.xyz/v1/score/batch" | jq
```

**Response:**
```json
{
  "results": [
    {"wallet": "addr1", "score": 78, "tier": "trusted"},
    {"wallet": "addr2", "score": 45, "tier": "neutral"}
  ],
  "stats": {
    "total": 2,
    "scored": 2,
    "not_found": 0,
    "avg_score": 61.5
  }
}
```

---

### GET /sybil/{wallet_address}

Check if wallet is part of a Sybil cluster.

**Example:**
```bash
curl -s -H "Authorization: Bearer $FAIRSCALE_API_KEY" \
  "https://api.fairscale.xyz/v1/sybil/7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU" | jq
```

**Response:**
```json
{
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "sybil_risk": "low",
  "confidence": 0.92,
  "cluster_id": null,
  "related_wallets": [],
  "indicators": []
}
```

---

### GET /credit/{wallet_address}

Creditworthiness assessment for DeFi lending.

**Example:**
```bash
curl -s -H "Authorization: Bearer $FAIRSCALE_API_KEY" \
  "https://api.fairscale.xyz/v1/credit/7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU" | jq
```

**Response:**
```json
{
  "wallet": "7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU",
  "credit_score": 720,
  "max_recommended_loan_usd": 5000,
  "risk_tier": "A",
  "collateral_ratio": 1.5,
  "repayment_history": {
    "loans_taken": 12,
    "loans_repaid": 12,
    "defaults": 0
  }
}
```

---

### GET /activity/{wallet_address}

Detailed activity breakdown.

**Query Parameters:**
- `days` (optional, default: 30): Analysis period

**Example:**
```bash
curl -s -H "Authorization: Bearer $FAIRSCALE_API_KEY" \
  "https://api.fairscale.xyz/v1/activity/7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU?days=90" | jq
```

---

### GET /compare

Compare two wallets.

**Query Parameters:**
- `wallet1` (required): First wallet address
- `wallet2` (required): Second wallet address

**Example:**
```bash
curl -s -H "Authorization: Bearer $FAIRSCALE_API_KEY" \
  "https://api.fairscale.xyz/v1/compare?wallet1=addr1&wallet2=addr2" | jq
```

---

## Rate Limits

| Tier | Requests/Day | Batch Size |
|------|--------------|------------|
| Free | 100 | 10 |
| Pro | 10,000 | 100 |
| Enterprise | Unlimited | 1000 |

## SDK Libraries

- **JavaScript/TypeScript:** `npm install @fairscale/sdk`
- **Python:** `pip install fairscale-sdk`
- **Rust:** `cargo add fairscale`

## Support

- Email: support@fairscale.xyz
- Discord: https://discord.gg/fairscale
- Twitter: @FairScaleXYZ
