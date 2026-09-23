# Methodology

[← Back to README](../README.md)

How every figure in this project was produced, which definitions were used, and which queries did what — including the ones that failed.

---

## 1. Scope and filters

Every query in the analysis applies the same window: `block_time >= TIMESTAMP '2026-01-01 00:00:00'` and `block_time < TIMESTAMP '2026-07-01 00:00:00'`, UTC.

Token identity:

| Chain | Filter |
| --- | --- |
| Ethereum | `contract_address = from_hex('a0b86991c6218b36c1d19d4a2e9eb0ce3606eb48')` |
| Solana | `token_mint_address = 'EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v'` |

Both are the native USDC issued by Circle. Bridged representations were measured and excluded (see limitations, point 5).

On the DEX tables, queries additionally filter on `block_date` alongside `block_time`. `block_date` is the partition key; without it Dune scans far more data than necessary. This was one of the changes that turned a timing-out query into a running one.

---

## 2. Definitions

**Transfer.** One row in `tokens.transfers` or `tokens_solana.transfers`. A single user action can produce several transfers.

**Transaction.** One `tx_hash` on Ethereum, one `tx_id` on Solana. Used only in the transaction-level composition (Q07), where the question is what a transaction as a whole was doing.

**Transfer leg.** A sending or receiving side of a transfer. A transfer has two legs. Used in the concentration and automated-activity analyses, where an address is credited for both what it sent and what it received.

**Gross transfer volume.** The sum of `amount_usd` over all transfers in scope. This is throughput, not net economic flow (limitations, point 2).

**Active address.** On Ethereum, an address appearing as sender or receiver of a USDC transfer in the period. On Solana, an *owner* (`from_owner` / `to_owner`), not a token account — a Solana user holds USDC in a token account owned by their wallet, and counting token accounts would inflate the figure.

**Active address, half-year total.** Deduplicated across the whole period. Never the sum of the six monthly figures (limitations, point 14).

**Net flow.** Everything an address received minus everything it sent, over the period. Not a balance: an address can end near zero and still show a large net flow if the gross flows were large in one direction.

**Parking sequence.** A wallet acquiring USDC in a DEX swap and later disposing of it in another DEX swap. The hold time is the interval between the two. If no disposal follows within the period, the sequence is recorded as "no re-entry".

---

## 3. Classification

Two independent classification layers are used, and they must not be added together.

**Layer 1, DEX vs. non-DEX.** A transfer is a DEX swap if its transaction appears in `dex.trades` / `dex_solana.trades` with USDC on either side of the trade. This layer partitions all transfers and sums to 100% per chain.

**Layer 2, issuer activity.** Mint and burn are identified from the transfer's counterparty: on Ethereum the null address and the known issuer addresses, on Solana the mint and burn instruction pattern. These transfers are a subset of the non-DEX group in layer 1.

The transaction-level composition (Q07) uses a third, mutually exclusive scheme: `DEX Swap`, `Mint`, `Burn . Redemption`, `Multi-action` and `Unidentified`, where a transaction containing several different action types is classified as multi-action. This one sums to 100% per chain.

`Unidentified` is a coverage statement, not a behavioural one. It means the transaction matched none of the labelled categories in Dune's curated tables.

In the chart legends the labels are shortened (`Mint`, `Burn . Redemption`, `Multi-action`) because the full names do not fit. The full names are in the underlying queries.

---

## 4. Size buckets

Transfers are bucketed by USD value into nine bands, numbered so that they sort correctly:

`00: <=$0 or NULL`, `01: <$1`, `02: $1-$10`, `03: $10-$100`, `04: $100-$1K`, `05: $1K-$10K`, `06: $10K-$100K`, `07: $100K-$1M`, `08: >=$1M`

Bucket `00` is kept rather than dropped. It holds transfers with a zero or missing USD value — 6.81% of Ethereum transfers and 0.53% of Solana transfers — and discarding them silently would change the denominators of every share in the section. When a share of "transfers below $1,000" is quoted in this project, bucket `00` is excluded and that is stated.

---

## 5. Concentration

Measured three ways, each as the share held by the largest 1, 10 and 100 addresses:

- share of sent volume
- share of received volume
- share of activity legs

Sent and received produce nearly identical figures on both chains, which is itself a sanity check — a large divergence would have indicated an aggregation error. Activity count behaves differently from both, and that difference is a finding rather than an artefact.

---

## 6. Automated activity

An address is classified as automated-like on the basis of transfer frequency and pattern regularity. This is a sensitivity analysis, not a claim about any individual address: the purpose is to show how much of each chain's totals would change if that group were removed. The results are reported with and without the group, so a reader can judge the effect.

---

## 7. Parking sequence reconstruction

This was the most demanding part of the project and it runs in two stages.

**Stage 1** identifies candidate wallets: addresses with USDC DEX activity in both directions during the period. Wallets above $1bn in DEX volume in either direction are excluded as infrastructure outliers.

**Stage 2** reconstructs the sequences for those candidates using a window function that, for each USDC acquisition, looks forward to the next disposal by the same wallet.

The original implementation used `ROWS BETWEEN 1 FOLLOWING AND UNBOUNDED FOLLOWING` on an ascending time order. That frame is poorly optimised in Trino and the query ran for over twenty minutes on the Large engine without completing. Rewriting it as a descending order with `ROWS BETWEEN UNBOUNDED PRECEDING AND 1 PRECEDING` — semantically identical, "all rows chronologically after this one" — made it complete. Dune's own efficient-query documentation only shows preceding-style frames, which is what pointed at the fix.

Hold times are bucketed as `<=1h`, `>1-6h`, `>6-24h`, `>1-7 days` and `>7 days / no reentry`. The last bucket mixes two things by design: sequences with a very long hold and sequences with no disposal at all within the period. They cannot be separated, because a wallet that would have sold on 2 July looks identical to one that never sells.

The column `% of re-entered sequences` is deliberately empty for that last bucket. Those sequences are not part of the re-entered population, and a zero would wrongly imply that they are.

---

## 8. Combined queries

Dune renders one widget from exactly one query. This analysis compares two chains, so every Ethereum/Solana pair would otherwise have produced two separate widgets side by side, leaving the comparison to the reader's eye.

The solution used here: a second query that carries the already computed, CSV-verified results of the source queries as literal `VALUES` rows, with a `chain` column, so both chains can be grouped into one chart or table. These queries read no table, cost effectively nothing to run, and cannot time out.

Two consequences worth stating plainly:

They are a presentation layer, not a computation. Each names its source queries in the SQL header comment, and those sources remain public and readable. The numbers are copied, not recalculated — which is exactly why every one of them was cross-checked before being copied (section 10).

Percentages inside those queries are computed in SQL, not typed in. Where a combined query reports shares, it derives them with `SUM(...) OVER (PARTITION BY chain)` from the literal values, so the shares cannot drift from the counts they describe.

Q10 is the exception: the two protocol queries genuinely recompute from `dex.trades` and `dex_solana.trades`, because the original results had expired and the data was not available in any export.

---

## 9. Engine and cost decisions

Dune meters credits by data scanned. Three practical rules came out of this project:

**Query composition is not caching.** `FROM query_XXXXXXX` is documented as a functional SQL view: the upstream query re-executes in full on every run. A cheap-looking aggregation on top of two expensive queries costs both of them again. Q07 (8804598) and Q09 (8804845) contain such references and are left un-rerun for that reason.

**For the heavy Solana joins, start on the Large engine.** A Small-engine run that times out after two minutes has spent credits and produced nothing, and the natural next step is Medium, then Large — three runs where one would have done.

**Reduce test runs, not test coverage.** The usual advice to check each step with a small query is expensive here. Testing was concentrated on the points where something was genuinely unknown: a column name, a schema assumption, whether a join would complete at all.

---

## 10. Cross-checks performed

Every figure used in the README and the report was verified against at least one independent query before publication.

| Check | Result |
| --- | --- |
| Q05 bucket counts sum to the Q02 chain totals | exact on both chains: 109,086,263 and 1,159,717,918 |
| Q05 bucket volumes sum to the Q18 gross volume | Ethereum differs by $0.01, Solana exact — floating-point rounding at trillion scale |
| Q06 DEX layer counts sum to the chain total | exact on both chains |
| Q06 DEX layer volumes sum to the gross volume | Ethereum differs by $3.88, Solana by $39.30 on a trillion-scale total |
| Q07 category shares sum to 100% per chain | yes |
| Q08 category shares exceed 100% | yes, as expected — the categories overlap (105.33% Ethereum, 123.25% Solana) |
| Q11 monthly issuer volumes summed vs the Q06 half-year issuer volumes | exact to the cent on all four series (Ethereum mint and burn, Solana mint and burn) |
| Q12 sent-volume vs received-volume concentration | agree to within 0.01pp on Ethereum and 0.05pp on Solana |
| Q13 total volume vs twice the gross volume | Ethereum matches to $2.15; Solana is $138,879,478,199.71 lower, which equals the Solana mint plus burn volume to within five cents — explained in limitations point 12 |
| Q17 re-entry rate recomputed from the bucket counts | 84.4637% Ethereum, 71.7204% Solana — matches the query output |
| Q02 non-positive rows vs the Q05 `<=$0 or NULL` bucket | identical on Ethereum, differ on Solana by 3,397,010 rows — explained in limitations point 13 |

Two of these checks did not reconcile at first sight. Both were traced: the Q13 gap is the issuer volume, because Solana mint and burn rows carry a NULL owner on one side and therefore contribute one leg instead of two, while Ethereum's null address is an ordinary value. The Q02/Q05 difference is the set of Solana transfers with a positive amount but no USD price. Neither required any figure to be corrected, and both are written up in the limitations rather than removed.

---

## 11. Query list

Every query is public. The ones marked as feeding the dashboard are listed in the README; this is the complete history, including failed, superseded and test versions, which are kept deliberately.

### Dashboard queries — @amir_1366

| Query | ID |
| --- | --- |
| Q02 Data Quality Check, Duplicates and Amount Deviation | 8808858 |
| Q03-Q04 Monthly Network Overview Combined | 8815962 |
| Q05 Transfer Size Distribution Combined | 8816422 |
| Q06 Transfer Classification Combined | 8816772 |
| Q07 Transaction-Level Activity Composition with Shares | 8817056 |
| Q08 Active Addresses by Category Combined | 8817422 |
| Q10-ETH Top DEX Protocols by Volume | 8818578 |
| Q10-SOL Top DEX Protocols by Volume | 8818586 |
| Q11 Issuer Monthly Trend Combined | 8818721 |
| Q12 Address Concentration Combined | 8819066 |
| Q13 Automated Activity Sensitivity Combined | 8819202 |
| Q14 Unidentified Category Top Counterparties Combined | 8819516 |
| Q15 Wallet-Level Net USDC Flows Combined | 8819754 |
| Q17 Parking Duration and Re-entry Rate | 8808809 |
| Q18 Final KPI Summary Table | 8819655 |

### Source and exploratory queries — @amirhoushang

| Query | ID |
| --- | --- |
| Q00-ETH Native USDC Coverage Check (Jan 2026) | 8799008 |
| Q00-ETH Symbol Sanity Check (Jan 2026) | 8799280 |
| Q00-SOL Row Count Coverage Check (Jan 2026) | 8799376 |
| Q00-SOL Date Range Coverage Check (Jan 2026) | 8799487 |
| Q01-SOL Bridged Variant Materiality Check (Jan 2026) | 8799541 |
| Q03-ETH Monthly Network Overview | 8799584 |
| Q03-SOL Monthly Network Overview | 8799625 |
| Q04-ETH Monthly Active Addresses | 8799645 |
| Q04-SOL Monthly Active Addresses (Jan 2026 test) | 8799750 |
| Q04-SOL Monthly Active Addresses | 8799817 |
| Q05-ETH Transfer Size Distribution (Jan 2026 test) | 8799904 |
| Q05-SOL Transfer Size Distribution (Jan 2026 test) | 8799923 |
| Q05-ETH Transfer Size Distribution | 8799942 |
| Q05-SOL Transfer Size Distribution | 8799950 |
| Q06-ETH Burn Sample for Manual Check (Jan 2026 test) | 8800009 |
| Q06-ETH Mint Burn CCTP Bridge Classification (Jan 2026 test) | 8799975 |
| Q06-SOL Null Owner Pre-Check (Jan 2026 test) | 8800017 |
| Q06-SOL Mint Burn Owner Sample (Jan 2026 test) | 8800041 |
| Q06-SOL Mint Burn Classification (Jan 2026 test) | 8800062 |
| Q06-ETH DEX Trades Schema Check (Jan 2026 test) | 8800078 |
| Q06-ETH DEX Swap Classification (Jan 2026 test) | 8800090 |
| Q06-SOL DEX Swap Classification (Jan 2026 test) | 8800108 |
| Q06-ETH DEX Swap Classification | 8803061 |
| Q06-SOL DEX Swap Classification | 8803074 |
| Q06-ETH Mint Burn CCTP Bridge Classification | 8803129 |
| Q06-SOL Mint Burn Classification | 8803192 |
| Q07-ETH Transaction-Level Composition (Jan 2026 test) | 8803287 |
| Q07-SOL Transaction-Level Composition (Jan 2026 test) | 8803316 |
| Q07-ETH Transaction-Level Composition | 8803356 |
| Q07-SOL Transaction-Level Composition (Jan–Mar 2026) | 8803612 |
| Q07-SOL Transaction-Level Composition (1–15 April 2026) | 8803815 |
| Q07-SOL Transaction-Level Composition (16–30 April 2026) | 8803872 |
| Q07-SOL Transaction-Level Composition (May 2026) | 8803892 |
| Q07-SOL Transaction-Level Composition (June 2026) | 8803929 |
| Q07-SOL Transaction-Level Composition Combined | 8804535 |
| Q07 Transaction-Level Composition Combined, both chains | 8804598 |
| Q08-ETH Active Addresses by Category | 8804654 |
| Q08-SOL Active Addresses by Category, Issuer Only | 8804681 |
| Q09 Category Comparison, Percentage Points | 8804845 |
| Q11-ETH Issuer and Cross-Chain Monthly Trend | 8805107 |
| Q11-SOL Issuer Monthly Trend | 8805115 |
| Q10-ETH Top DEX Protocols by Volume | 8805007 |
| Q10-SOL Top DEX Protocols by Volume | 8805038 |
| Q12A-ETH Address Concentration, Sent Volume | 8805167 |
| Q12A-SOL Address Concentration, Sent Volume | 8805185 |

The Solana transaction-level composition appears five times because the full-period query could not complete: it was split by time range and the parts were recombined. That split is visible in the list on purpose.

### Source queries — @mira_1987

| Query | ID |
| --- | --- |
| Q08-SOL Active Addresses by Category, DEX Swap and Unidentified | 8807083 |
| Q12B-ETH Address Concentration, Received Volume | 8805856 |
| Q12B-SOL Address Concentration, Received Volume | 8805812 |
| Q12C-ETH Address Concentration, Activity Count | 8805631 |
| Q12C-SOL Address Concentration, Activity Count | 8805700 |
| Q13-ETH Automated Activity Sensitivity | 8806084 |
| Q13-SOL Automated Activity Sensitivity | 8806108 |
| Q14-ETH Unidentified Category Top Counterparties | 8805989 |
| Q14-SOL Unidentified Category Top Counterparties | 8806240 |
| Q15-ETH Wallet-Level Net USDC Flows | 8806295 |
| Q15-SOL Wallet-Level Net USDC Flows | 8806327 |
| Q16-ETH Parking Candidate Wallets, Stage 1 | 8807224 |
| Q16-SOL Parking Candidate Wallets, Stage 1 | 8807256 |
| Q18 Final KPI Summary Table | 8807042 |

### Source queries — @amir_1987

| Query | ID |
| --- | --- |
| Q16-ETH Parking Sequence Reconstruction, Stage 2 | 8808433 |
| Q16-SOL Parking Sequence Reconstruction, Stage 2 | 8808480 |

### A note on Q10

The two Q10 queries exist twice. The originals (8805007, 8805038) on @amirhoushang are readable but no longer hold a stored result — Dune discards execution results after a period of inactivity. Rather than re-run them on an account without credits, they were forked to @amir_1366 as 8818578 and 8818586 and executed there. The SQL is identical; only the forks carry current results.

---

## 12. Problems encountered and how they were solved

Recorded because they cost time and credits, and because anyone working with these tables will meet them.

**Solana active addresses timed out repeatedly.** An anti-join between `tokens_solana.transfers` and `dex_solana.trades` over six months exceeds the Small engine's two-minute limit and also failed on Medium. It was solved by combining three changes: pre-filtering candidates with a cheap `GROUP BY`, adding the `block_date` partition filter to the JOIN condition rather than only to the WHERE clause, replacing an `IN` subquery with an explicit `INNER JOIN`, and running on the Large engine.

**A window-frame direction blocked the parking analysis.** Described in section 7.

**Query composition was mistaken for caching.** An aggregation query built on two expensive parents hung in the planning phase every time. The cause was that both parents were re-executing on every run. The fix was to stop composing and carry the verified results as literal values.

**A binary column was mistaken for text.** Stage 1 of the parking analysis returns `address` as `VARBINARY`, which Dune renders as a `0x…` string in a CSV export. Stage 2 applied `from_hex(substr(address, 3))` to it and failed with a hex decoding error. The fix was to drop the conversion and join on binary equality directly.

**A wrong column name produced a false data-quality finding.** Described in limitations, point 11.

Three of these five were only resolved after going back to the Dune documentation instead of reasoning from the error message.
