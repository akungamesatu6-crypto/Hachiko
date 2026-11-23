# HACHIFUND — Agent Skill

> **LIVE — contract deployed 2026-11-23.** Fund contract
> `0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c`, tx
> `0x786b413d960bc7a13a6fea550a30b4b5116a6e879de28592b050a78e9be4697b`
> (block 135266415). Minting is open.

HACHI Fund is a community fund on BNB Smart Chain (chainId 56). 1,000
shares total. Each share costs 0.0015 BNB and gives 1,000,000 $HACHI.
When the fund is full (1.5 BNB), it buys tokens with loyal communities —
tokens that stay, like Hachi. There is no guaranteed price and no
guaranteed market.

## Contracts (LIVE)

| Item | Value |
|------|-------|
| Fund contract (ERC20 + fund) | `0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c` |
| Deployed address (CREATE2, salt 0) | `0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c` |
| Treasury / owner | `0x7379CA652f68fd1daAc11b7b87bb6B222fD129fa` |
| Token | Hachi Fund (HACHI), 18 decimals |
| Chain | BNB Smart Chain (chainId 56) |

## How to mint (give this to your agent)

```
Mint HACHI Fund shares on BNB Smart Chain (chainId 56) by calling mint(uint256 shares) on this contract: 0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c

Shares: 1
Price per share: 0.0015 BNB
Transaction value: 0.0015 BNB (0.0015 BNB x 1)
Result: 1,000,000 HACHI sent to my wallet.

Steps:
1. Read sharesSold() and confirm at least 1 shares are left out of 1000.
2. Preview the call: contract address, shares, BNB value and estimated gas.
3. Wait for me to reply "confirm" before sending anything.
4. After the transaction, show the tx hash and my new HACHI balance.

Never ask for my seed phrase or private key. Use only the contract address above.
```

For 10 shares: Shares: 10, Transaction value: 0.015 BNB
(0.0015 BNB x 10), Result: 10,000,000 HACHI. Confirm at least 10
shares are left.

## Contract functions

- `mint(uint256 shares)` payable — exact value required:
  `shares * 0.0015 BNB`. Mints `shares * 1,000,000 HACHI` to the caller
  and forwards the full BNB to the treasury in the same tx.
- `sharesSold()` view — shares sold so far (0–1,000).
- `maxShares()` view — 1,000. `pricePerShare()` view — 0.0015 BNB.
  `tokensPerShare()` view — 1,000,000 HACHI.
- Errors: `ZeroShares()`, `SoldOut(sharesSold, requested, maxShares)`,
  `WrongValue(sent, required)`, `ForwardFailed()`.

## Rules for the agent

- Read `sharesSold()` FIRST and confirm shares are left before any mint.
- Preview the full call (address, shares, BNB value, estimated gas)
  and wait for the user to reply "confirm" before sending anything.
- NEVER ask for a seed phrase or private key. Use only the contract
  address above.
- After the tx, show the tx hash and the new HACHI balance.
- There is no guaranteed price or trading. The payment goes to the
  treasury wallet, not into a liquidity pool.

## Links

- Page: https://hachifund.vercel.app/
- X: @HachiFund — https://x.com/HachiFund (account being created)
- Treasury on BscScan: https://bscscan.com/address/0x7379CA652f68fd1daAc11b7b87bb6B222fD129fa
- Repo: https://github.com/akungamesatu6-crypto/Hachiko
