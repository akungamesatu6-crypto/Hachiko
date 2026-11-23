# HACHIFUND v2

**Status: PREVIEW — v2 contracts NOT deployed. Page v2 staged locally;
the live v1 page at https://hachifund.vercel.app/ stays until deploy.**

HACHI Fund is a community fund on BNB Smart Chain, Hachiko-themed.
Your first share is **FREE** (1 per wallet); paid shares cost
**0.0015 BNB each → 1,000,000 $HACHI**. **2,222 shares total: 1,222
free + 1,000 paid** (1.5 BNB from the paid pool when full). When the
fund is full, it buys tokens with loyal communities — tokens that stay,
like Hachi.

- **Token:** Hachi Fund (HACHI), 18 decimals, BNB Smart Chain (chainId 56)
- **HACHI token:** `0x0000000000000000000000000000000000000000` (PREVIEW placeholder)
- **Predicted token address (CREATE2, salt 0):** `0xb11f494CE956276FcF8E5602f57D70b5b10e79Aa`
- **Free fund:** `0x0000000000000000000000000000000000000000` (PREVIEW placeholder)
- **Predicted free address (CREATE2, salt 0):** `0x3c6CAA75E1da5BA266833325434CDB21c5CE6051`
- **Paid fund:** `0x0000000000000000000000000000000000000000` (PREVIEW placeholder)
- **Predicted paid address (CREATE2, salt 0):** `0x3750e5fd6b9c996f255a1Ec29b961849226a17ef`
- **Treasury / owner:** `0x7379CA652f68fd1daAc11b7b87bb6B222fD129fa`
- **X:** @HachiFund (https://x.com/HachiFund — account being created)
- **Language:** EN + 日本語 (EN primary)

## How it works

There is no wallet connect on the page. Copy a prompt, give it to your
agent, and it calls `mintFree()` on the free contract (free share) or
`mint(uint256 shares)` on the paid contract (paid shares). Your agent
reads `freeSold()` / `paidSold()` first, previews the call, waits for
your "confirm", then shows the tx hash and your new $HACHI balance.
Paid revenue forwards to the treasury in the same transaction.

## Honest terms

- No guaranteed price. $HACHI can be traded only if a market exists.
- Paid mint payment goes to the treasury wallet, not into a liquidity pool.
- No NFT, no marketplace, no tiers — this model is a fund, not a collection.
- No pause switch: what deploys is what runs.
- v1 (HachiFund `0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c`) is superseded
  at 0 shares sold — v1 is immutable and has no free-mint function.

## Files

- `contracts/` — Hardhat project (HachiToken.sol, HachiFundFree.sol,
  HachiFundPaid.sol; 21/21 v2 tests pass; `contracts/hachifund_v2_predictions.md`
  + three `*_inputdata.txt` files). v1 HachiFund.sol untouched (13/13 pass).
- `v2/index.html` — the v2 single page (EN + 日本語, live free/paid counters)
- `v2/assets/` — hero.jpg + emblem.jpg (Hachiko artwork by Tania, reused from v1)
- `BRIEF.md` — full spec · `SKILL.md` — agent mint instructions ·
  `llms.txt` — short summary

## Deploy (NOT done)

Deploy three contracts via the Arachnid CREATE2 proxy, in order:
token (`hachitoken_inputdata.txt`), free fund (`hachifundfree_inputdata.txt`),
paid fund (`hachifundpaid_inputdata.txt`). The token deploys owned by the
paired wallet 0x05f63E... (interim); that wallet MUST call `setMinter`
on the token for each fund (2 calls), then `transferOwnership` to
0x7379...9fa — minting reverts `NotMinter` until then. Then fill the
real contract addresses into the
page + docs, point the shares watch at the two new fund contracts, push,
and replace the live v1 page. Deploy needs the user's same-turn confirmation.
