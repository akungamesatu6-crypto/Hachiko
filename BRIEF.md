# HACHIFUND v2 — Brief

**Status: PREVIEW — v2 contracts NOT deployed. Build done 2026-11-23;
deploy awaits the user's same-turn "Deploy".**
Built from the user's order 2026-11-23 19:09 WIB ("1.222 gratis +
1.000 berbayar") and 19:11 WIB ("Pisahkan saya kontraknya, namun tetap
dalam satu halaman web" — separate the contracts, still ONE page).
v2 supersedes v1 (HachiFund `0x7b4a3e9D485AC07A6B38C66A9c226a2817d8a34c`,
deployed 2026-11-23, abandoned at 0 shares sold — v1 is immutable:
MAX_SHARES 1000 constant, no free-mint function, so it cannot gain a
free mint).

## Reference (third-party, inspiration only)

SAFUFUND — https://safufund.netlify.app/ — studied 2026-11-23. A single
minimal page, no wallet connect: visitors copy a prompt into their AI
agent, the agent calls `mint(uint256 shares)` on BSC. Verified on-chain
at study time: contract `0x00c8Fe84695E5456fBaaFEFcd15265da2FD3342B`,
`sharesSold()` = 181/1,000. SAFUFUND's contract was NOT copied — only its
observed behaviour (mint/sharesSold, 0.0015 BNB/share → 1,000,000
tokens/share, 1,000 shares). HachiFund v1 + v2 code is original, written
for this project.

## Identity

- **Name:** HACHIFUND (Hachi Fund). **Token:** $HACHI ("Hachi Fund", HACHI).
- **Theme:** Hachiko — the loyal Akita who waited at the station.
  Tagline: "The loyal fund. Mint with your agent."
- **Story:** when the fund is full, it buys tokens with loyal communities
  — tokens that stay, like Hachi.
- **Colours:** dark brown-black background (#17100a), warm amber/gold
  accents (#e8a33d), cream text. Artwork by Tania: hero Akita at a
  lantern-lit station at dusk + Akita-face emblem (assets/, reused
  unchanged from v1 — no new artwork generated for v2).
- **Language:** EN + 日本語 (EN primary, JA secondary).
- **X:** @HachiFund — account NOT yet created; the user creates it.
  The page links https://x.com/HachiFund and labels it honestly.
- **Chain:** BNB Smart Chain (chainId 56).

## Economics (v2 — user-confirmed 2026-11-23)

| Item | Value |
|------|-------|
| Free pool | 1,222 shares, 1 per wallet, 0 BNB (`mintFree()`) |
| Paid pool | 1,000 shares @ 0.0015 BNB each (+ gas) |
| Per share (free or paid) | 1,000,000 $HACHI |
| Shares total | 2,222 (MAX_FREE 1,222 + MAX_PAID 1,000) |
| Fund full | 1.5 BNB collected (paid pool), 2,222,000,000 $HACHI minted |
| Revenue | 100% of every paid mint forwards to the treasury in the same tx |
| Treasury / owner | `0x7379CA652f68fd1daAc11b7b87bb6B222fD129fa` (user's NEW wallet) |
| Fees / royalty | None. No platform fee, no royalty, no tax. |
| Guaranteed price / market | NONE — stated honestly on the page and in the FAQ. $HACHI trades only if a market exists. |

## Contracts (three — HachiToken.sol, HachiFundFree.sol, HachiFundPaid.sol)

One shared HACHI token + two independent fund contracts. Constructor
args: token(owner); free(token, owner); paid(token, owner). Owner is the
user's NEW wallet in all three.

- **HachiToken** (ERC20 + Ownable): name "Hachi Fund", symbol HACHI,
  18 decimals, supply starts 0. `setMinter(address, bool)` owner-only
  registers fund contracts; `mint(address to, uint256 amount)` only
  registered minters (else `NotMinter`). No pre-mint, no public mint.
- **HachiFundFree** (Ownable): `mintFree()` — 0 BNB, one share per
  wallet (`hasClaimedFree` mapping); second claim reverts
  `AlreadyClaimed`, full pool reverts `FreeSoldOut`. Mints 1,000,000
  HACHI via the token. Views: `freeSold()` (0–1,222),
  `maxFreeShares()` (1,222), `hasClaimedFree(address)`,
  `tokensPerShare()` (1,000,000e18). Non-payable (BNB attached reverts).
- **HachiFundPaid** (Ownable): `mint(uint256 shares)` payable —
  requires shares > 0, `paidSold + shares <= 1000`, `msg.value ==
  shares * 0.0015 ether` (exact — under- and overpayment both revert).
  Mints `shares * 1,000,000 HACHI` via the token, `paidSold += shares`,
  emits `SharesMinted(buyer, shares, value)`, then forwards the FULL
  msg.value to the owner (treasury). Views: `paidSold()` (0–1,000),
  `maxPaidShares()` (1,000), `pricePerShare()` (0.0015 ether),
  `tokensPerShare()` (1,000,000e18).
- Combined: total shares = `freeSold + paidSold` (max 2,222); total
  HACHI = `(freeSold + paidSold) * 1,000,000`, max 2,222,000,000.
- **No pause switch, no price change, no cap change** — deliberate,
  minimal like the reference. What deploys is what runs; any change
  needs a new deployment. Owner can only transferOwnership (Ownable)
  and (token) setMinter.
- **No Agent ID gate on either mint:** the agent-only flow is enforced
  by the page/prompt (no wallet connect on the page), exactly like v1.
  Anyone calling the contracts directly can mint/claim — honest
  behaviour of this model, not a bug. Documented here so nobody "fixes"
  it later.
- Checks-effects-interactions: pool counters and claim flags are updated
  BEFORE the token mint and (paid) the BNB forward, so a re-entrant call
  cannot double-spend. No ReentrancyGuard — kept minimal on purpose.
- Tests: 21/21 pass (`npx hardhat test test/hachifund_v2.test.js`),
  including the full 1,222-claim free-cap fill. v1 tests still 13/13.
- CREATE2 predictions (salt 0, Arachnid proxy
  `0x4e59b44847b379578588920cA78FbF26c0B4956C`):
  **HachiToken `0xb11f494CE956276FcF8E5602f57D70b5b10e79Aa`** (682,565 gas),
  **HachiFundFree `0x3c6CAA75E1da5BA266833325434CDB21c5CE6051`** (322,242 gas),
  **HachiFundPaid `0x3750e5fd6b9c996f255a1Ec29b961849226a17ef`** (403,655 gas);
  total 1,408,462 gas ≈ 0.0000704231 BNB @ 0.05 gwei. See
  `contracts/hachifund_v2_predictions.md` + the three `*_inputdata.txt`.

## Page (v2/index.html — single page, no market.html)

Same Hachiko design as v1 (dark brown-black, amber/gold, cream text),
EN + 日本語 toggle (EN default). v2 changes only:

Sections: PREVIEW banner (v2 contracts not deployed yet) · header
(emblem + HACHIFUND + EN/日本語 toggle) · hero (hero.jpg, badge "Mint
with your agent", concept paragraph — now mentions the free share and
2,222 total) · LIVE status (total sold X/2,222 + progress bar + free
claimed X/1,222 + paid sold X/1,000 + BNB collected from paid only +
shares left, read live on-chain via `freeSold()` + `paidSold()` with RPC
fallback; honest preview state at 0) · "The HACHI terms" (7 numbered
points, free mint first, split explained, treasury address SHOWN) ·
"Copy the prompt" (three buttons: "Copy prompt: 1 free mint"
(mintFree template), "Copy prompt: 1 mint", "Copy prompt: 10 mints"
(paid templates); default shown prompt is the free mint) · FAQ (7:
v1's 5 + "How do I get the free share?" + "Why are there two
contracts?") · footer (X @HachiFund, treasury BscScan link, v2 contract
status).

Rules kept: NO wallet connect anywhere. NO dead buttons. NO Vercel/GitHub
mentions on the page. NO invented counts — live counters read the chain
and show real values. Inline JS passes `node --check` and was verified
with a stubbed-DOM functional test (33 checks: preview rendering, live
reads, prompt templates, EN/JA switch).

## Deploy + wiring (NOT done — needs the user's same-turn "Deploy")

1. Deploy HachiToken via the proxy with `contracts/hachitoken_inputdata.txt`.
2. Deploy HachiFundFree via the proxy with `contracts/hachifundfree_inputdata.txt`.
3. Deploy HachiFundPaid via the proxy with `contracts/hachifundpaid_inputdata.txt`.
4. Wiring (REQUIRED): the token deploys owned by the paired wallet
   0x05f63E20097c9360D9D68277b90b4a8A8cF8D7C1 (INTERIM — the CLI can only
   sign as the paired wallet, never as 0x7379...9fa). That wallet calls
   `setMinter` on the token for EACH fund contract (2 calls), then
   `transferOwnership(0x7379CA652f68fd1daAc11b7b87bb6B222fD129fa)` —
   `mintFree()` and `mint()` both revert `NotMinter` until then. Safe:
   the token owner is not the treasury (paid mints forward BNB to the
   funds' owner from the first mint). No other wiring: the funds' owner
   is set in their constructors, minting opens at deploy (no switch).
5. After deploy + wiring: replace the three zero-address placeholders in
   v2/index.html, SKILL.md, llms.txt, README.md with the real contract
   addresses; verify on BscScan (token owner, free maxFreeShares 1,222,
   paid maxPaidShares 1,000, pricePerShare 0.0015 BNB, tokensPerShare
   1,000,000, freeSold = 0, paidSold = 0); point the shares watch at the
   two new fund contracts (it still watches v1); push to GitHub (repo
   Hachiko); replace the live v1 page on Vercel; verify the live page +
   live counters.

## Deliberately left out

- No NFT, no marketplace, no tiers/rarity — this model has none.
- No presale, no claim contract, no staking, no redemption flow.
- No Agent ID registry gate — free mint is EOA-tracked (1 per address),
  same model as v1's public mint.
- No "not affiliated with Binance" line — not needed here (no Binance
  name is used anywhere in this project).
- No X account created (user's job). No GitHub push, no Vercel change —
  all pending the user's orders.
