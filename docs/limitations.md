# Limitations

[← Back to README](../README.md)


What the figures in this project cannot tell you, and where the underlying data is incomplete. Nothing here was discovered after the fact to excuse a weak result — most of these were written down before the analysis ran, and the ones that were not are marked as such.

---

## 1. An address is not a person

Every count of "active addresses" is a count of addresses, never of people. One person can control many addresses; one address — an exchange wallet, a smart contract, a Solana program — can serve many people. No statement in this project may be read as a user count, and none is phrased that way.

## 2. Gross volume is throughput, not economic value

"Gross transfer volume" counts every transfer leg, including internal hops inside a protocol and intermediary steps that move the same dollars several times. It measures how much USDC moved across the ledger, not how much economic value changed hands. A single swap routed through three pools produces three transfers.

## 3. Cross-chain burn and mint pairs cannot be reconstructed

Ethereum's `tx_hash` and Solana's `tx_id` share no common key, and the curated tables carry no cross-chain correlation identifier. Individual CCTP burn/mint pairs therefore cannot be matched across the two chains with the available data. Cases that cannot be attributed cleanly are reported under `Issuer — Unclassified` rather than being assigned to bridging.

No CCTP burn events were detected on Ethereum in this period at all. That is an absence of labelled data, not proof that no bridging occurred.

## 4. Category labelling is incomplete and not symmetric between the chains

The categories come from Dune's curated labels. Coverage differs by chain. A low or zero value in one category on one chain can mean the label is missing, not that the activity is absent. This matters most for the `Unidentified` category, which is the largest category by value on Solana (83.09%) and the largest by transaction count on Ethereum (73.91%). `Unidentified` means "matched none of the labelled categories" — nothing more.

## 5. The bridged-variant threshold is a fixed cut

Bridged USDC variants were excluded because they fell below a 1% materiality threshold measured over the period. A variant sitting just below that line is not tracked separately even if it grew towards the end of the period.

## 6. A wallet-to-wallet transfer is not a payment

Without verified merchant or payment labels, no "payments" category was constructed. Transfers between two unlabelled addresses are reported as unidentified, not as commerce.

## 7. Parking sequences are patterns, not intentions

The parking analysis can show that a wallet bought USDC in a swap and sold it again a measurable time later. It cannot show why. No statement about motivation, risk perception or market view can be derived from it.

## 8. The parking candidate population is dominated by automated trading

This is the most important caveat in the project.

Candidate wallets were selected as addresses with USDC DEX activity in both directions, after removing extreme outliers above $1bn in DEX volume. What remains is still not an organic user population: 1,229 Ethereum wallets produced 3,527,079 sequences (2,870 per wallet over six months) and 1,334 Solana wallets produced 126,097,891 sequences (94,526 per wallet). Those are machine rates, not human ones.

The section is therefore a description of active trading wallets, not of typical USDC holders, and it is labelled that way on the dashboard. It should not be generalised.

## 9. The system-address exclusion list is curated, not complete

Concentration figures depend on which addresses are treated as infrastructure. That list was assembled by hand and is certainly not exhaustive.

## 10. "Automated-like" is an indicator, not proof

The automated-activity classification is a heuristic based on transfer frequency and pattern. An address flagged as automated-like is not a confirmed bot, and an address not flagged is not confirmed human.

## 11. Solana's amount columns have different scales

`tokens_solana.transfers` carries three amount columns: `amount` (raw, not decimal-adjusted), `amount_display` (decimal-adjusted) and `amount_usd` (USD value, derived from the decimal-adjusted figure). Comparing `amount_usd` against `amount` produces a spurious deviation of about 99.5%.

This is recorded here because it actually happened during the work: the first run of the data-quality check used the wrong column and produced an apparent Solana data-quality catastrophe. It was caught before it reached any published result. Every Solana query in this project uses `amount_display`.

## 12. Q13's Solana volume base is smaller than twice the gross volume, by exactly the issuer volume

Q13 measures volume as sent plus received legs, so its total should be about twice the gross transfer volume.

On Ethereum it is: $18,011,384,095,729.55 against 2 × $9,005,692,047,865.85 = $18,011,384,095,731.70, a difference of $2.15 on eighteen trillion.

On Solana it is 2.17% lower: $6,248,870,445,423.55 against an expected $6,387,749,923,623.26. The gap is $138,879,478,199.71.

That gap is the Solana mint plus burn volume, which is $138,879,478,199.66 — a difference of five cents.

The cause is a NULL on one side of issuer transfers. On Solana a mint has `from_owner = NULL` and a burn has `to_owner = NULL`, so those rows contribute one leg to an address-level aggregation instead of two. On Ethereum the equivalent counterparty is the null *address* `0x0000…0000`, which is an ordinary value rather than SQL NULL, so both legs count and the totals reconcile exactly.

This is a consequence of how the two chains represent issuance, not a data defect, and no Q13 figure needs correcting. It is kept here because the 2.17% gap looks alarming until it is traced.

## 13. Zero and missing USD values are counted differently in two places

Two figures look like they should match and do not, on Solana:

- The data-quality check (Q02) counts 2,765,097 Solana rows with a token amount of zero or less, which is 0.2384% of all transfers.
- The size distribution (Q05) puts 6,162,107 Solana transfers into the `<=$0 or NULL` bucket, which is 0.5313%.

Both are correct. They measure different things: Q02 counts a non-positive token amount, while the Q05 bucket also catches transfers that have a positive token amount but no USD value at all. The difference — 3,397,010 transfers — is the set of Solana transfers with a real amount but no price attached.

On Ethereum the two figures are identical (7,433,376 rows, 6.8142%), so this affects Solana only.

That figure of 3,397,010 is derived by subtraction, not measured directly. Confirming it would need one query counting Solana rows where `amount_display > 0 AND amount_usd IS NULL`, which is a full scan of a billion-row table. It has not been run, and the number is presented as a derived quantity.

## 14. Monthly active-address figures cannot be summed

An address active in three months appears in three monthly figures. The half-year totals used in this project (12,767,473 on Ethereum, 23,534,326 on Solana) are deduplicated across the whole period and come from Q13, not from adding up the monthly values in Q03-Q04.

## 15. Protocol names are not comparable across chains

Ethereum and Solana have entirely different DEX landscapes, and Dune's project labels differ accordingly. The two protocol charts are presented separately for that reason. Ranking Uniswap against Bisonfi would not mean anything.

---

## Data-dependent checks, with results

These were open questions before the analysis and are closed now.

| Check | Result |
| --- | --- |
| Continuous monthly coverage, both chains, Jan–Jun 2026 | no gaps found |
| Bridged variants above the 1% materiality threshold | no — 0.0021% of Solana transfer count |
| Duplicate rows in the transfer tables | none: 109,086,263 of 109,086,263 distinct on Ethereum, 1,159,717,918 of 1,159,717,918 on Solana |
| Rows where `amount_usd` deviates more than 1% from the token amount | zero on both chains |
| Non-positive amount rows | Ethereum 7,433,376 (6.8142%), Solana 2,765,097 (0.2384%) — reported separately, not discarded |
| Parking reconstruction feasible (≥500 candidate wallets per chain) | yes, 1,229 and 1,334 — but see limitation 8 |
| Categories present on both chains | yes, with the CCTP exception in limitation 3 |

---

## Statements this project does not make

It says nothing about the identity, location or intent of any address holder.

It offers no causal explanation for any observed difference between the two chains. Ethereum and Solana differ in fee structure, block time, ecosystem composition and user base, and none of those were measured here.

It does not claim that unidentified activity is unimportant or illegitimate — only that the available labels do not cover it.

It does not extrapolate on-chain USDC activity to the real economy, to stablecoins in general, or to USDC on other chains.

It does not claim that the parking candidate wallets represent typical USDC users. They do not.
