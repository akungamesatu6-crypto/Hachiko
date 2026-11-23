# HACHIFUND v2 — Agent Skill

> **PREVIEW — v2 contracts NOT deployed yet.** The contract addresses
> below are zero-address placeholders. Do NOT attempt a real mint until
> the addresses are filled in after deploy. v2 supersedes v1 (HachiFund
> `0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c`, abandoned at 0 shares
> sold — v1 is immutable and has no free-mint function).

HACHI Fund is a community fund on BNB Smart Chain (chainId 56). 2,222
shares total: **1,222 FREE** (1 per wallet) + **1,000 PAID** at 0.0015
BNB each. Every share — free or paid — gives 1,000,000 $HACHI. When the
fund is full (1.5 BNB from the paid pool), it buys tokens with loyal
communities — tokens that stay, like Hachi. There is no guaranteed price
and no guaranteed market.

## Contracts (PREVIEW)

| Item | Value |
|------|-------|
| HACHI token (ERC20) | `0x0000000000000000000000000000000000000000` (PLACEHOLDER — fill at deploy) |
| Predicted token address (CREATE2, salt 0) | `0xb11f494CE956276FcF8E5602f57D70b5b10e79Aa` |
| Free fund (mintFree) | `0x0000000000000000000000000000000000000000` (PLACEHOLDER — fill at deploy) |
| Predicted free address (CREATE2, salt 0) | `0x3c6CAA75E1da5BA266833325434CDB21c5CE6051` |
| Paid fund (mint) | `0x0000000000000000000000000000000000000000` (PLACEHOLDER — fill at deploy) |
| Predicted paid address (CREATE2, salt 0) | `0x3750e5fd6b9c996f255a1Ec29b961849226a17ef` |
| Treasury / owner | `0x7379CA652f68fd1daAc11b7b87bb6B222fD129fa` |
| Token owner at deploy (interim) | `0x05f63E20097c9360D9D68277b90b4a8A8cF8D7C1` (paired wallet; signs setMinter ×2, then transferOwnership to the treasury) |
| Token | Hachi Fund (HACHI), 18 decimals |
| Chain | BNB Smart Chain (chainId 56) |

Deploy order: token first, then the free fund, then the paid fund.
**After deploy the paired wallet (0x05f63E... — the token's interim
owner) must call `setMinter` on the token for EACH fund contract
(2 calls), then `transferOwnership` to 0x7379...9fa — minting does not
work until then.** (The CLI can only sign as the paired wallet.)

## How to claim the free share (give this to your agent)

```
Claim my FREE HACHI Fund share on BNB Smart Chain (chainId 56) by calling mintFree() on this contract: 0x0000000000000000000000000000000000000000

Shares: 1 (free, 1 per wallet)
Transaction value: 0 BNB (only network gas)
Result: 1,000,000 HACHI sent to my wallet.

Steps:
1. Read freeSold() and hasClaimedFree(my address) and confirm free shares are left and I have not claimed yet.
2. Preview the call: contract address and estimated gas.
3. Wait for me to reply "confirm" before sending anything.
4. After the transaction, show the tx hash and my new HACHI balance.

Never ask for my seed phrase or private key. Use only the contract address above.
```

## How to mint paid shares (give this to your agent)

```
Mint HACHI Fund shares on BNB Smart Chain (chainId 56) by calling mint(uint256 shares) on this contract: 0x0000000000000000000000000000000000000000

Shares: 1
Price per share: 0.0015 BNB
Transaction value: 0.0015 BNB (0.0015 BNB x 1)
Result: 1,000,000 HACHI sent to my wallet.

Steps:
1. Read paidSold() and confirm at least 1 paid shares are left out of 1000.
2. Preview the call: contract address, shares, BNB value and estimated gas.
3. Wait for me to reply "confirm" before sending anything.
4. After the transaction, show the tx hash and my new HACHI balance.

Never ask for my seed phrase or private key. Use only the contract address above.
```

For 10 paid shares: Shares: 10, Transaction value: 0.015 BNB
(0.0015 BNB x 10), Result: 10,000,000 HACHI. Confirm at least 10
paid shares are left.

## Contract functions

- `HachiToken.setMinter(address minter, bool allowed)` — owner only.
  Registers a fund contract as a minter. `mint(address to, uint256
  amount)` — only registered minters; anyone else reverts `NotMinter`.
- `HachiFundFree.mintFree()` — 0 BNB. One free share per wallet
  (`hasClaimedFree` tracked on-chain); a second claim reverts
  `AlreadyClaimed`, a full pool reverts `FreeSoldOut`.
- `HachiFundFree.freeSold()` view — free shares claimed (0–1,222).
  `maxFreeShares()` view — 1,222. `hasClaimedFree(address)` view.
- `HachiFundPaid.mint(uint256 shares)` payable — exact value required:
  `shares * 0.0015 BNB`. Mints `shares * 1,000,000 HACHI` to the caller
  and forwards the full BNB to the treasury in the same tx.
- `HachiFundPaid.paidSold()` view — paid shares sold (0–1,000).
  `maxPaidShares()` view — 1,000. `pricePerShare()` view — 0.0015 BNB.
  `tokensPerShare()` view — 1,000,000 HACHI (both funds).
- Errors: `ZeroShares()`, `SoldOut(paidSold, requested, maxPaidShares)`,
  `WrongValue(sent, required)`, `ForwardFailed()`, `AlreadyClaimed(claimer)`,
  `FreeSoldOut(freeSold, maxFreeShares)`, `NotMinter(caller)`.

## Rules for the agent

- For a free claim, read `freeSold()` AND `hasClaimedFree(your address)`
  FIRST — confirm free shares are left and you have not claimed yet.
- For a paid mint, read `paidSold()` FIRST and confirm paid shares are
  left before any mint.
- Preview the full call (address, shares, BNB value, estimated gas)
  and wait for the user to reply "confirm" before sending anything.
- NEVER ask for a seed phrase or private key. Use only the contract
  addresses above.
- After the tx, show the tx hash and the new HACHI balance.
- There is no guaranteed price or trading. The payment goes to the
  treasury wallet, not into a liquidity pool.

## Links

- Page: https://hachifund.vercel.app/ (v1 live now; v2 replaces it at deploy)
- X: @HachiFund — https://x.com/HachiFund (account being created)
- Treasury on BscScan: https://bscscan.com/address/0x7379CA652f68fd1daAc11b7b87bb6B222fD129fa
- Repo: https://github.com/akungamesatu6-crypto/Hachiko
- v1 (superseded, 0 mints): https://bscscan.com/address/0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c
