# FairScale Solana Skill

Agent skill for querying Solana wallet reputation scores via the FairScale API.

## What This Does

Once installed and configured, anyone chatting with your agent can ask things like:

- "What's the FairScore for 7xKXtg2CW87d97TXJSDpbD5jBkheTqA83TZRuJosgAsU?"
- "Is this wallet trustworthy?"
- "Does this look like a Sybil or bot?"
- "Check the reputation before I trade with this address"

Your agent will query the FairScale API and return a detailed analysis.

## Installation

### Using npx skills (recommended)

```bash
npx skills add fairscale/solana-skill
```

### Using ai-agent-skills

```bash
npx ai-agent-skills install fairscale/solana-skill
```

### Using openskills

```bash
npx openskills install fairscale/solana-skill
```

### Manual installation

Clone this repo into your agent's skills directory:

**Claude Code:**
```bash
git clone https://github.com/fairscale/solana-skill ~/.claude/skills/fairscale
```

**OpenClaw:**
```bash
git clone https://github.com/fairscale/solana-skill ~/.openclaw/skills/fairscale
```

**Cursor:**
```bash
git clone https://github.com/fairscale/solana-skill .cursor/skills/fairscale
```

## Configuration (Required)

After installing the skill, you need to set up your API key:

1. **Purchase an API key** at [sales.fairscale.xyz](https://sales.fairscale.xyz)
2. **Check your email** for the API key delivery
3. **Set the environment variable** on the machine running your agent:
   ```bash
   export FAIRSCALE_API_KEY="zpka_your_key_here"
   ```

   For OpenClaw, you can also configure it via:
   ```bash
   openclaw config set skills.entries.fairscale.apiKey "zpka_your_key_here"
   ```

Once configured, your agent can make FairScale API calls autonomously whenever users ask about wallet reputation.

## What You Can Do

Once installed, ask your agent:

- "Check the reputation of wallet `7xKXtg...`"
- "Is this wallet trustworthy for a trade?"
- "What's the FairScore for this address?"
- "Does this wallet look like a Sybil?"
- "Show me the feature breakdown for this wallet"

## About FairScale

FairScale is reputation infrastructure for Solana. Our dynamic neural network retrains daily to provide real-time reputation scores combining:

- On-chain behavior analysis
- Transaction patterns
- Token holdings
- Protocol interactions
- Social presence data

Used by protocols for Sybil detection, airdrop filtering, and creditworthiness assessment.

## Links

- [FairScale Website](https://fairscale.xyz)
- [Get API Key](https://sales.fairscale.xyz)
- [API Documentation](https://api2.fairscale.xyz/docs)
- [$FAIR Token](https://fairscale.xyz/token)
- [Twitter @FairScaleXYZ](https://twitter.com/FairScaleXYZ)

## License

MIT
