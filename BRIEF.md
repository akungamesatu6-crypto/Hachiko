# HACHIFUND — Brief

**Status: LIVE — contract deployed 2026-11-23, page live at https://hachifund.vercel.app/.**
Built 2026-11-23 from the user's order: "Buatkan project serupa" (like
SAFUFUND) "namanya tema dan warna halaman sesuaikan dengan nama Hachiko
(hachi)". Spec confirmed by the user 2026-11-23 18:19 WIB: "Betul semua
nya sudah ok, semua pendapatan masuk ke dompet baru sedangkan bayarannya
kita pakai baw baru" — all revenue to the NEW wallet, payments use the
new BAW.

## Reference (third-party, inspiration only)

SAFUFUND — https://safufund.netlify.app/ — studied 2026-11-23. A single
minimal page, no wallet connect: visitors copy a prompt into their AI
agent, the agent calls `mint(uint256 shares)` on BSC. Verified on-chain
at study time: contract `0x00c8Fe84695E5456fBaaFEFcd15265da2FD3342B`,
`sharesSold()` = 181/1,000. SAFUFUND's contract was NOT copied — only its
observed behaviour (mint/sharesSold, 0.0015 BNB/share → 1,000,000
tokens/share, 1,000 shares). HachiFund.sol is original code written for
this project.

## Identity

- **Name:** HACHIFUND (Hachi Fund). **Token:** $HACHI ("Hachi Fund", HACHI).
- **Theme:** Hachiko — the loyal Akita who waited at the station.
  Tagline: "The loyal fund. Mint with your agent."
- **Story:** when the fund is full, it buys tokens with loyal communities
  — tokens that stay, like Hachi.
- **Colours:** dark brown-black background (#17100a), warm amber/gold
  accents (#e8a33d), cream text. Artwork by Tania: hero Akita at a
  lantern-lit station at dusk + Akita-face emblem (assets/).
- **Language:** EN + 日本語 (EN primary, JA secondary).
- **X:** @HachiFund — account NOT yet created; the user creates it.
  The page links https://x.com/HachiFund and labels it honestly.
- **Chain:** BNB Smart Chain (chainId 56).

## Economics (confirmed — same as SAFUFUND)

| Item | Value |
|------|-------|
| Price | 0.0015 BNB per share (+ gas) |
| Per share | 1,000,000 $HACHI |
| Shares total | 1,000 (MAX_SHARES) |
| Fund full | 1.5 BNB collected, 1,000,000,000 $HACHI minted |
| Revenue | 100% of every mint forwards to the treasury in the same tx |
| Treasury / owner | `0x7379CA652f68fd1daAc11b7b87bb6B222fD129fa` (user's NEW wallet) |
| Fees / royalty | None. No platform fee, no royalty, no tax. |
| Guaranteed price / market | NONE — stated honestly on the page and in the FAQ. $HACHI trades only if a market exists. |

## Contract (one contract — HachiFund.sol)

ERC20 + Ownable, combined token + fund logic (mirrors SAFUFUND's
single-contract design). Constructor takes ONE arg: `owner_`.

- `mint(uint256 shares)` payable: requires shares > 0, `sharesSold +
  shares <= 1000`, `msg.value == shares * 0.0015 ether` (exact — under-
  and overpayment both revert). Mints `shares * 1,000,000 HACHI` to the
  caller, `sharesSold += shares`, emits `SharesMinted(buyer, shares,
  value)`, then forwards the FULL msg.value to the owner (treasury).
- `sharesSold()` public view. Views: `maxShares()` (1,000),
  `pricePerShare()` (0.0015 ether), `tokensPerShare()` (1,000,000e18).
- No initial mint: total supply = `sharesSold * 1,000,000 HACHI`, max
  1,000,000,000 HACHI. No minter role — tokens are minted ONLY by mint().
- **No pause switch, no price change, no cap change** — deliberate,
  minimal like the reference. What deploys is what runs; any change
  needs a new deployment. Owner can only transferOwnership (Ownable).
- **No Agent ID gate on mint():** the agent-only flow is enforced by the
  page/prompt (no wallet connect on the page), exactly like the reference
  model. Anyone calling the contract directly can mint — honest behaviour
  of this model, not a bug. Documented here so nobody "fixes" it later.
- Checks-effects-interactions: sharesSold is updated and tokens minted
  BEFORE the BNB forward, so a re-entrant call cannot double-spend. No
  ReentrancyGuard — kept minimal on purpose.
- Tests: 13/13 pass (`npx hardhat test`).
- CREATE2 prediction (salt 0, Arachnid proxy
  `0x4e59b44847b379578588920cA78FbF26c0B4956C`):
  **HachiFund `0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c`**,
  est. 801,486 gas ≈ 0.0000400743 BNB @ 0.05 gwei. See
  `contracts/predictions.md` + `contracts/hachifund_inputdata.txt`.

## Page (index.html — single page, no market.html)

Minimal like SAFUFUND, Hachiko-styled, mobile-first. Dark brown-black,
amber/gold accents, cream text. EN + 日本語 toggle (EN default).

Sections: live banner · header (emblem +
HACHIFUND + EN/日本語 toggle) · hero (hero.jpg, badge "Mint with your
agent", concept paragraph) · LIVE status (shares sold X/1,000 + progress
bar + BNB collected, read live on-chain via `sharesSold()` with RPC
fallback; live state, 0 sold at deploy) · "The HACHI terms" (6 numbered
points, treasury address SHOWN — an improvement over SAFUFUND, which
hides it) · "Copy the prompt" (prompt textbox + "Copy prompt: 1 mint" /
"Copy prompt: 10 mints" buttons; prompt uses the live contract address) · FAQ (5, mirroring SAFUFUND + "Who runs the
fund?") · footer (X @HachiFund, treasury BscScan link, contract status).

Rules kept: NO wallet connect anywhere. NO dead buttons. NO Vercel/GitHub
mentions on the page. NO invented counts — live counter reads the chain
and shows its real value (0 sold at deploy). Inline JS passes `node --check`.

## Deploy + wiring (DONE 2026-11-23 ~18:55 WIB)

1. Deploy HachiFund via the proxy with `contracts/hachifund_inputdata.txt`.
2. Wiring: NONE — owner is set in the constructor, mint opens at deploy
   (no switch). First `mint()` works immediately after the deploy tx.
3. DONE: placeholder replaced with 0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c in index.html
   (root + push/), SKILL.md, llms.txt, README.md; verified on BscScan
   (owner 0x7379...9fa, maxShares 1000, pricePerShare 0.0015 BNB,
   tokensPerShare 1,000,000, sharesSold = 0); pushed to GitHub (repo
   Hachiko); Vercel project hachifund live; live page + live counter
   verified.
4. Deployed from paired wallet 0x05f63E20097c9360D9D68277b90b4a8A8cF8D7C1
   (CLI pairing connected that wallet, NOT 0x7379...9fa — user confirmed
   deploy from it); tx 0x786b413d960bc7a13a6fea550a30b4b5116a6e879de28592b050a78e9be4697b, block 135266415, cost ~0.000058 BNB;
   owner is 0x7379...9fa as designed.

## Deliberately left out

- No NFT, no marketplace, no tiers/rarity — this model has none.
- No presale, no claim contract, no staking, no redemption flow.
- No live shares-sold count on SAFUFUND — HACHIFUND ADDS it (on-chain read).
- No "not affiliated with Binance" line — not needed here (no Binance
  name is used anywhere in this project).
- No X account created (user's job), no Vercel project, no GitHub push —
  all pending the user's orders.
