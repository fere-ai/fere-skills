# fere-skills

## What FereAI is

[FereAI](https://www.fereai.xyz/) is infrastructure for on-chain research and crypto trading. An agent researches markets and executes from a non-custodial wallet: gasless swaps and bridges, limit orders, take-profit and stop-loss, Polymarket, and Hyperliquid perps. Chains include Ethereum, Solana, Base, Arbitrum, BNB Chain, and Polygon.

This skill is the procedure for that work. It is meant to be used together with the [FereAI MCP server](https://www.fereai.xyz/mcp), which is the live connection to the account.

## Skills installation

### Skills usage

The [skills CLI](https://github.com/vercel-labs/skills) installs this skill for the agents you have. It lands in the current project. `-g` installs it for every project.

```bash
npx skills add https://github.com/fere-ai/fere-skills
```

```bash
# This skill only
npx skills add https://github.com/fere-ai/fere-skills --skill fere

# Claude Code and Codex, no prompts
npx skills add https://github.com/fere-ai/fere-skills --skill fere -a claude-code -a codex -y

# Available in every project
npx skills add https://github.com/fere-ai/fere-skills --skill fere -g
```

`claude-code` writes `.claude/skills/`. `codex` writes `.agents/skills/` in a project, or `~/.codex/skills/` with `-g`.

Claude Code can also install the plugin directly:

```
/plugin marketplace add fere-ai/fere-skills
/plugin install fere-skills@fere-skills
```

Codex reads `.codex-plugin/plugin.json` in this repo, which points at `./skills/`.

## FereAI MCP

Add the same server to any MCP client (Claude, Cursor, ChatGPT, Codex). Streamable HTTP is `https://api.fereai.xyz/mcp`. SSE is `https://api.fereai.xyz/mcp/sse`. Both auth modes below are accepted on `/mcp`, `/mcp/sse`, and `/mcp/messages`. Details are in [auth.md](https://api.fereai.xyz/auth.md).

```json
{
  "mcpServers": {
    "FereAI": {
      "type": "http",
      "url": "https://api.fereai.xyz/mcp"
    }
  }
}
```

The skill tells the agent when to call the tools, and when a write needs a holdings check before it is treated as filled.

### OAuth mode

Use this when the agent acts for a signed-in person. The client runs Clerk OAuth and sends the JWT as `Authorization: Bearer eyJ...`. Tools act on that person's FereAI account.

### Headless mode

Use this for an unattended agent. There is no consent screen and no OAuth redirect. Auth is an Ed25519 keypair, and the bearer is an `agt_*` token for the wallet that key registered. This skill's `scripts/fere.py` is what registers that key.

The MCP server mints that token when both are set:

```bash
export FERE_AGENT_ID=agt_...
export FERE_SECRET_KEY=<base64 Ed25519 secret>
```

Or send `Authorization: Bearer agt_...` on the MCP request yourself.
