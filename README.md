# Bouncer

**Not every token gets in.** Token risk checks for AI agents on Solana, tuned for pump.fun launches.

Before an agent buys a token, it asks Bouncer and gets a score from 0 to 100, a verdict (**ADMITTED**, **HOLD** or **DENIED**) and every reason behind it.

- Site: https://bouncersol.fun
- API: https://bouncer-api.orchard-api.workers.dev
- X: https://x.com/bouncer_sol

## What it checks
- Mint and freeze authority, and Token-2022 traps: permanent delegate, transfer hooks, transfer fees, non-transferable, default-frozen accounts
- The pump.fun launch block: the dev's buy and other wallets bundled into the same block, and what they still hold
- How much of the launch buy the dev has sold, and how many tokens the dev wallet launched before
- Top 10 holders with the bonding curve and pools excluded
- Copycats (same ticker, much bigger market cap), organic volume, liquidity, activity and socials

Every score starts at 100 and each red flag subtracts a published number of points. See the [scoring docs](https://bouncersol.fun/#/docs/scoring).

## Use it
MCP (Claude Code):
```
claude mcp add --transport http bouncer https://bouncer-api.orchard-api.workers.dev/mcp
```
REST:
```
curl https://bouncer-api.orchard-api.workers.dev/v1/check/<mint>
```
Free: 30 checks a day per IP. API keys are paid by burning $BOUNCER.

## Run your own
`worker/` is a Cloudflare Worker with a D1 database.
```
cd worker
npm install
npx wrangler d1 create bouncer            # paste the id into wrangler.toml
npx wrangler d1 execute bouncer --remote --file=schema.sql
npx wrangler secret put SOL_RPC_URL     # a Solana mainnet RPC url (e.g. Helius)
npx wrangler deploy
node test.mjs                           # tests (SOL_RPC_URL=... to avoid public RPC limits)
```
`site/index.html` is the whole site in one file; set `CONFIG.api` at the bottom.

## Data sources
Solana RPC (mint, pump.fun bonding curve, largest token accounts, launch-block transactions) and Jupiter's token API (market data, holder count, organic score, dev launch history).

Bouncer shows automated risk signals from public data. It is not financial advice.
