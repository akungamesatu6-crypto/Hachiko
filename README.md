# HACHIFUND

**Status: PREVIEW — contracts NOT deployed, page NOT live.** Local build only.

HACHI Fund is a community fund on BNB Smart Chain, Hachiko-themed.
Mint shares with your agent: **0.0015 BNB per share → 1,000,000 $HACHI**.
**1,000 shares total** (1.5 BNB when full). When the fund is full, it
buys tokens with loyal communities — tokens that stay, like Hachi.

- **Token:** Hachi Fund (HACHI), 18 decimals, BNB Smart Chain (chainId 56)
- **Fund contract:** `0x0000000000000000000000000000000000000000` (PREVIEW placeholder)
- **Predicted address (CREATE2, salt 0):** `0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c`
- **Treasury / owner:** `0x7379CA652f68fd1daAc11b7b87bb6B222fD129fa`
- **X:** @HachiFund (https://x.com/HachiFund — account being created)
- **Language:** EN + 日本語 (EN primary)

## How it works

There is no wallet connect on the page. Copy the prompt, give it to your
agent, and it calls `mint(uint256 shares)` on the fund contract. Your
agent reads `sharesSold()` first, previews the call, waits for your
"confirm", then shows the tx hash and your new $HACHI balance. Revenue
forwards to the treasury in the same transaction.

## Honest terms

- No guaranteed price. $HACHI can be traded only if a market exists.
- Payment goes to the treasury wallet, not into a liquidity pool.
- No NFT, no marketplace, no tiers — this model is a fund, not a collection.
- No pause switch: what deploys is what runs.

## Files

- `contracts/` — Hardhat project (HachiFund.sol, 13/13 tests pass,
  `contracts/predictions.md`, `contracts/hachifund_inputdata.txt`)
- `index.html` — the single page (EN + 日本語, live shares-sold counter)
- `assets/` — hero.jpg + emblem.jpg (Hachiko artwork by Tania)
- `BRIEF.md` — full spec · `SKILL.md` — agent mint instructions ·
  `llms.txt` — short summary
- `push/` — byte-identical copies of the page + docs (repo-root layout)

## Deploy (NOT done)

Deploy the single contract via the Arachnid CREATE2 proxy with
`contracts/hachifund_inputdata.txt`. No wiring needed — owner is set in
the constructor and mint opens at deploy. Then fill the real contract
address into the page + docs, push, and create the hosting project.
Deploy needs the user's same-turn confirmation.
