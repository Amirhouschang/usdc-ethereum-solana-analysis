# Findings

Every figure below comes from a public Dune query listed in [docs/methodology.md](../docs/methodology.md). Period: 1 January to 30 June 2026, native USDC only.

---

## 1. Network overview

| | Ethereum | Solana |
| --- | --- | --- |
| Transfers | 109,086,263 | 1,159,717,918 |
| Gross transfer volume | $9,005,692,047,865.85 | $3,193,874,961,811.63 |
| Unique active addresses (H1) | 12,767,473 | 23,534,326 |

Solana carried 10.63 times Ethereum's transfer count and 35.47% of its value.

Monthly detail:

| Month | ETH transfers | SOL transfers | ETH volume | SOL volume | ETH addresses | SOL addresses |
| --- | --- | --- | --- | --- | --- | --- |
| Jan | 18,511,475 | 190,054,688 | $1,585,002,156,096 | $457,408,009,646 | 3,243,440 | 3,663,621 |
| Feb | 15,611,586 | 169,816,425 | $1,702,565,022,756 | $861,628,524,391 | 2,734,079 | 4,153,494 |
| Mar | 18,063,590 | 187,480,280 | $1,855,411,045,904 | $563,712,846,383 | 3,012,210 | 3,999,068 |
| Apr | 16,331,498 | 181,187,379 | $1,181,165,710,912 | $479,202,764,201 | 2,230,727 | 4,494,759 |
| May | 18,861,768 | 185,209,537 | $983,240,377,836 | $371,986,833,374 | 2,478,100 | 5,831,860 |
| Jun | 21,706,346 | 245,969,609 | $1,698,307,734,360 | $459,935,983,816 | 2,690,259 | 6,288,736 |

Two patterns hold across all six months. Ethereum's monthly volume is always above Solana's, and Solana's transfer count never falls below 9.82 times Ethereum's (the monthly ratio ranges from 9.82 in May to 11.33 in June).

The address trend runs in opposite directions: Solana's monthly active addresses rise from 3.66 million in January to 6.29 million in June, while Ethereum's fall from 3.24 million to 2.69 million, with a low of 2.23 million in April.

Monthly figures cannot be summed to the half-year totals — the same address is active in several months. The H1 totals above are deduplicated across the whole period.

---

## 2. Transfer size distribution

### Ethereum

| Bucket | Transfers | Share | Volume | Share |
| --- | --- | --- | --- | --- |
| `<=$0 or NULL` | 7,433,376 | 6.81% | $0 | 0.00% |
| `<$1` | 40,744,197 | 37.35% | $1,238,376 | 0.00001% |
| `$1–$10` | 5,689,301 | 5.22% | $27,284,663 | 0.0003% |
| `$10–$100` | 15,395,776 | 14.11% | $683,728,474 | 0.0076% |
| `$100–$1K` | 20,962,427 | 19.22% | $8,080,999,062 | 0.0897% |
| `$1K–$10K` | 12,161,801 | 11.15% | $39,270,466,691 | 0.4361% |
| `$10K–$100K` | 4,489,526 | 4.12% | $153,864,423,118 | 1.7085% |
| `$100K–$1M` | 1,746,823 | 1.60% | $535,117,672,139 | 5.9420% |
| `>=$1M` | 463,036 | 0.42% | $8,268,646,235,342 | 91.8158% |

### Solana

| Bucket | Transfers | Share | Volume | Share |
| --- | --- | --- | --- | --- |
| `<=$0 or NULL` | 6,162,107 | 0.53% | $0 | 0.00% |
| `<$1` | 319,724,849 | 27.57% | $57,074,284 | 0.0018% |
| `$1–$10` | 170,818,468 | 14.73% | $777,645,480 | 0.0243% |
| `$10–$100` | 276,482,428 | 23.84% | $11,499,357,387 | 0.3600% |
| `$100–$1K` | 305,323,751 | 26.33% | $115,063,337,578 | 3.6026% |
| `$1K–$10K` | 69,347,320 | 5.98% | $188,739,078,551 | 5.9094% |
| `$10K–$100K` | 7,073,310 | 0.61% | $179,670,094,494 | 5.6255% |
| `$100K–$1M` | 4,599,032 | 0.40% | $1,609,227,152,671 | 50.3848% |
| `>=$1M` | 186,653 | 0.02% | $1,088,841,221,367 | 34.0915% |

**Where the median sits.** Cumulative transfer share reaches 50% inside the `$10–$100` bucket on both chains — Ethereum crosses from 49.38% to 63.49% there, Solana from 42.83% to 66.67%. The typical transfer is small on both chains.

**Where the value sits.** Excluding the zero/NULL bucket, transfers below $1,000 are 75.90% of Ethereum transfers and 0.0976% of Ethereum volume; on Solana, 92.47% of transfers and 3.9888% of volume.

The two chains part company at the top of the distribution. Ethereum concentrates 91.82% of its value in transfers of $1 million and above. Solana's largest band is $100,000–$1 million at 50.38%, with the million-plus band at 34.09%.

---

## 3. Category composition

### By transaction

| Category | Ethereum | Solana |
| --- | --- | --- |
| DEX Swap | 12,015,732 (24.83%) | 435,682,674 (69.22%) |
| Mint | 219,306 (0.45%) | 466 (0.0001%) |
| Burn . Redemption | 23,611 (0.05%) | 399,566 (0.06%) |
| Multi-action | 364,668 (0.75%) | 65,167 (0.01%) |
| Unidentified | 35,762,372 (73.91%) | 193,232,458 (30.70%) |
| **Total** | **48,385,689** | **629,380,331** |

### By volume

| Category | Ethereum | Solana | ETH minus SOL |
| --- | --- | --- | --- |
| DEX Swap | 58.974% | 12.561% | +46.413 pp |
| Issuer / Burn / Redemption | 1.175% | 2.219% | −1.045 pp |
| Issuer — Unclassified (Mint) | 1.165% | 2.129% | −0.964 pp |
| Bridge / CCTP | 0.000% | — | — |
| Unidentified | 38.686% | 83.090% | −44.404 pp |

### By address

Categories overlap here — one address can appear in several — so the shares sum to more than 100% (105.33% on Ethereum, 123.25% on Solana).

| Category | Ethereum | Share | Solana | Share |
| --- | --- | --- | --- | --- |
| DEX Swap | 752,402 | 5.89% | 7,128,420 | 30.29% |
| Mint | 68,607 | 0.54% | 4 | 0.00002% |
| Burn . Redemption | 14 | 0.0001% | 149,410 | 0.63% |
| Unidentified | 12,627,266 | 98.90% | 21,728,789 | 92.33% |

**The inversion.** Solana's USDC is predominantly a swap instrument by transaction count (69.22%) but those swaps carry only 12.56% of its value. Ethereum's swaps are a quarter of transactions and nearly 60% of value. The same asset does two different jobs on the two chains.

**A handful of issuer addresses.** 14 addresses on Ethereum and 4 on Solana account for all mint and burn activity in the period.

**No CCTP burns were detected on Ethereum.** That is an absence of labelled data, not evidence that no bridging happened.

---

## 4. Protocols and issuance

### Top 10 DEX protocols by USDC volume — Ethereum

| Rank | Protocol | Trades | Volume |
| --- | --- | --- | --- |
| 1 | uniswap | 9,615,756 | $94,341,300,801 |
| 2 | fluid | 434,175 | $29,006,041,032 |
| 3 | curve | 1,009,263 | $14,049,383,264 |
| 4 | balancer | 318,282 | $10,633,261,536 |
| 5 | ekubo | 1,283,622 | $8,905,221,252 |
| 6 | dodo | 435,160 | $7,706,632,296 |
| 7 | native | 326,105 | $6,906,822,344 |
| 8 | maverick | 185,173 | $2,249,290,836 |
| 9 | metric | 74,843 | $1,498,465,654 |
| 10 | swaap | 101,370 | $1,178,188,371 |

### Top 10 DEX protocols by USDC volume — Solana

| Rank | Protocol | Trades | Volume |
| --- | --- | --- | --- |
| 1 | bisonfi | 54,419,000 | $62,698,256,283 |
| 2 | humidifi | 63,326,376 | $50,588,210,672 |
| 3 | whirlpool | 38,689,970 | $29,131,748,721 |
| 4 | tessera | 42,051,918 | $25,710,338,962 |
| 5 | alphaq | 51,251,286 | $24,003,935,263 |
| 6 | manifest | 30,551,072 | $23,272,425,745 |
| 7 | meteora | 70,438,630 | $22,844,836,097 |
| 8 | solfi | 44,550,519 | $22,677,135,493 |
| 9 | goonfi | 50,028,411 | $15,946,962,221 |
| 10 | raydium | 59,071,633 | $15,487,355,383 |

Ethereum's DEX activity is dominated by one protocol: Uniswap alone holds 53.46% of the top-10 volume. Solana's is not — the largest protocol holds 21.45%, and the top ten are within a factor of four of each other.

The trade sizes differ by an order of magnitude. Across the top ten, the average USDC trade is $12,803 on Ethereum and $580 on Solana.

Protocol names are not comparable across chains; the two lists describe entirely different ecosystems.

### Issuance

| | Ethereum | Solana |
| --- | --- | --- |
| Mint volume, H1 | $104,945,445,469 | $67,995,571,750 |
| Burn volume, H1 | $105,781,242,785 | $70,883,906,450 |
| Mint monthly range | $15.85bn – $18.15bn (factor 1.14) | $9.78bn – $12.70bn (factor 1.30) |
| Burn monthly range | $13.51bn – $20.92bn (factor 1.55) | $10.12bn – $14.31bn (factor 1.41) |

Burn slightly exceeds mint on both chains over the half-year. No month shows a supply shock.

The transfer counts behind those volumes differ sharply in character. Ethereum mint is many small operations (459,780 transfers for $104.9bn); Solana mint is a few very large ones (466 transfers for $68.0bn).

---

## 5. Address concentration

Share of the chain total held by the largest 1, 10 and 100 addresses:

| Measure | Chain | Top 1 | Top 10 | Top 100 |
| --- | --- | --- | --- | --- |
| Sent volume | Ethereum | 32.96% | 67.89% | 84.85% |
| Sent volume | Solana | 7.01% | 17.35% | 38.66% |
| Received volume | Ethereum | 32.96% | 67.89% | 84.86% |
| Received volume | Solana | 7.02% | 17.34% | 38.61% |
| Activity legs | Ethereum | 2.87% | 12.33% | 29.05% |
| Activity legs | Solana | 4.42% | 15.90% | 45.62% |

Two things stand out.

Ethereum's **value** is extraordinarily concentrated — one address moves a third of all USDC volume, ten addresses move two thirds.

Solana's **activity** is the more concentrated of the two — its top 100 addresses produce 45.62% of all transfer legs against Ethereum's 29.05%. Solana has fewer very large value holders but more very busy ones.

Sent and received concentration agree to within 0.05 percentage points on both chains, which is what one would expect if the aggregation is correct.

---

## 6. Automated activity

| | Ethereum | Solana |
| --- | --- | --- |
| Active addresses | 12,767,473 | 23,534,326 |
| Automated-like addresses | 12,433 | 62,611 |
| Share of addresses | 0.0974% | 0.2660% |
| Total volume (sent + received legs) | $18,011,384,095,730 | $6,248,870,445,424 |
| Volume from automated-like addresses | $16,439,246,007,287 | $2,830,319,571,404 |
| Share of volume | 91.27% | 45.29% |
| Volume excluding automated-like | $1,572,138,088,445 | $3,418,550,874,020 |
| Transfer legs | 218,172,526 | 2,318,963,086 |
| Automated-like legs | 125,212,312 | 2,052,255,358 |
| Share of legs | 57.39% | 88.50% |

The two chains are mirror images. On Ethereum a group of 12,433 addresses — one in a thousand — produces nine tenths of the value but only 57% of the transfer legs. On Solana the automated-like group produces 88.50% of all legs but under half the value.

Automation dominates **value** on Ethereum and **frequency** on Solana.

Removing the group entirely leaves $1.57tn of Ethereum volume and $3.42tn of Solana volume — an inversion of the headline ranking, and the single strongest argument for not reading gross volume as economic activity.

Note on the volume base: Q13 counts sent and received legs, so its total should be about twice the gross transfer volume. On Ethereum it matches to $2.15. On Solana it is $138.88bn lower — exactly the Solana mint plus burn volume, because issuer transfers carry a NULL owner on one side and contribute one leg instead of two. See [limitations](limitations.md), point 12.

---

## 7. Unidentified counterparties

The largest receiving addresses inside the Unidentified category.

### Ethereum

| Rank | Address | Received | Transfers |
| --- | --- | --- | --- |
| 1 | 0xbbbbbbbbbb9cc5e90e3b3af64bdaf62c37eeffcb | $481,910,040,600 | 318,694 |
| 2 | 0x00eb00c6f847740000884d00e03f00c761998feb | $319,409,388,376 | 4,133 |
| 3 | 0x55fe002aeff02f77364de339a1292923a15844b8 | $154,450,302,541 | 73,173 |
| 4 | 0x31173ed183e5a9450c3671018ec4d770c8a8bf18 | $139,909,077,297 | 2,075 |
| 5 | 0xa9d1e08c7793af67e9d92fe308d5697fb81d3e43 | $107,476,409,317 | 571,219 |

### Solana

| Rank | Address | Received | Transfers |
| --- | --- | --- | --- |
| 1 | 41zCUJsKk6cMB94DDtm99qWmyMZfp4GkAhhuz4xTwePu | $219,015,601,957 | 688,403 |
| 2 | 5tzFkiKscXHK5ZXCGbXZxdw7gTjjD1mBwuoFbhUvuAi9 | $64,704,033,565 | 616,348 |
| 3 | 3ADzk5YDP9sgorvPSs9YPxigJiSqhgddpwHwwPwmEFib | $34,933,179,390 | 9,417 |
| 4 | CwqbisCpBWVbceWGky36ADbitqHC1nmpE51FZzLy9NzV | $34,600,407,178 | 872 |
| 5 | 7zuYE4xGLn9mgBPGGyKV8PJoAUKMbc5v9AGPVTPRcbqx | $29,311,739,244 | 1,088 |

The single largest Ethereum unidentified counterparty received $481.9bn — 5.35% of the entire Ethereum USDC volume for the half-year — across 318,694 transfers.

Two distinct profiles appear in both lists: high-frequency addresses with hundreds of thousands of transfers, and low-frequency addresses moving tens of billions across a few hundred. The first look like routing or market-making infrastructure, the second like settlement or custody. Neither can be confirmed from the data.

The Ethereum entry `0x0000…0000` at rank 7 is the null address and represents burns.

---

## 8. Wallet-level net flows

Net flow is everything an address received minus everything it sent over the period. It is not a balance.

### Largest net accumulators

| Chain | Address | Net flow |
| --- | --- | --- |
| Ethereum | 0x38aaef3782910bdd9ea3566c839788af6ff9b200 | +$1,312,685,558 |
| Ethereum | 0x0000000000000000000000000000000000000000 | +$835,797,317 |
| Ethereum | 0x37305b1cd40574e4c5ce33f8e8306be057fd7341 | +$664,550,265 |
| Solana | 7s1da8DduuBFqGra5bJBjpnvL5E9mGzCuMk1Qkh4or2Z | +$119,445,270 |
| Solana | 4Dg89gRmz8rUrTRiBP6XzfWJWtEuAxEYAWXn64AE3xvi | +$89,987,055 |
| Solana | 81w96XvKAZZFmmuzaohSQUyxtGnAPJmkjwoBFBmtytUj | +$55,406,891 |

### Largest net depleters

| Chain | Address | Net flow |
| --- | --- | --- |
| Ethereum | 0x420ef1f25563593af5fe3f9b9d3bc56a8bd8c104 | −$1,000,286,371 |
| Ethereum | 0x98c23e9d8f34fefb1b7bd6a91b7ff122f4e16f5c | −$942,171,923 |
| Ethereum | 0x3b4d794a66304f130a4db8f2551b0070dfcf5ca7 | −$816,641,990 |
| Solana | H8BgJgae6qhMtf7BM2JtddywSQt11WdxHHxkGLNX5hss | −$1,539,417,150 |
| Solana | 7VHUFJHWu2CuExkJcJrzhQPJ2oygupTWkL2A2For4BmE | −$796,873,928 |
| Solana | 4jtZwKHXD7vToiiPyqwa9467FQc19Mcy62wSFHijZqqE | −$135,288,230 |

Ethereum's net movements are spread across many addresses in the hundreds of millions. Solana's are dominated by one: the largest depleter alone accounts for −$1,539,417,150, more than the other nine addresses in the top-ten depleter list combined (−$1,521,991,362).

Ethereum's second-largest accumulator is the null address, which is where burned USDC goes.

---

## 9. Parking and settlement sequences

A parking sequence is a wallet buying USDC in a DEX swap and later selling it again — USDC used as a temporary resting asset between trades.

Candidate wallets: addresses with USDC DEX activity in both directions, after removing outliers above $1bn in DEX volume.

### Ethereum — 1,229 wallets, 3,527,079 sequences, $17.19bn parked

| Hold time | Sequences | Wallets | Parked volume | Share of all | Share of re-entered |
| --- | --- | --- | --- | --- | --- |
| `<=1h` | 2,551,488 | 831 | $9.35bn | 72.34% | 85.65% |
| `>1–6h` | 276,500 | 803 | $1.78bn | 7.84% | 9.28% |
| `>6–24h` | 89,436 | 764 | $1.04bn | 2.54% | 3.00% |
| `>1–7 days` | 61,677 | 794 | $1.11bn | 1.75% | 2.07% |
| `>7 days / no reentry` | 547,978 | 1,229 | $3.91bn | 15.54% | — |

Re-entry rate: **84.46%**

### Solana — 1,334 wallets, 126,097,891 sequences, $58.50bn parked

| Hold time | Sequences | Wallets | Parked volume | Share of all | Share of re-entered |
| --- | --- | --- | --- | --- | --- |
| `<=1h` | 89,876,832 | 1,334 | $40.62bn | 71.28% | 99.38% |
| `>1–6h` | 255,988 | 931 | $1.10bn | 0.20% | 0.28% |
| `>6–24h` | 135,315 | 854 | $0.88bn | 0.11% | 0.15% |
| `>1–7 days` | 169,758 | 667 | $0.80bn | 0.13% | 0.19% |
| `>7 days / no reentry` | 35,659,998 | 1,289 | $15.10bn | 28.28% | — |

Re-entry rate: **71.72%**

**Reading these numbers.** On both chains, when USDC is parked it is parked briefly: 85.65% of Ethereum re-entries and 99.38% of Solana re-entries happen within an hour. Solana's distribution is close to binary — either under an hour, or never.

The `>7 days / no reentry` bucket deliberately mixes long holds with sequences that had no disposal before 30 June. The two cannot be separated.

**This is not a user population.** 1,229 Ethereum wallets produced 3,527,079 sequences (2,870 per wallet) and 1,334 Solana wallets produced 126,097,891 (94,526 per wallet) in six months. These are machine rates. The section describes automated trading wallets and nothing more general.

---

## 10. Data quality

| | Ethereum | Solana |
| --- | --- | --- |
| Transfer rows | 109,086,263 | 1,159,717,918 |
| Distinct rows | 109,086,263 | 1,159,717,918 |
| Duplicate rate | 0.0000% | 0.0000% |
| Rows with amount ≤ 0 | 7,433,376 (6.8142%) | 2,765,097 (0.2384%) |
| Rows with >1% USD deviation | 0 | 0 |

No duplicates and no USD/token mismatches across 1,268,804,181 rows.

Rows with a zero or missing amount are reported separately rather than dropped. They are counted in the transfer totals and contribute nothing to volume.

One discrepancy worth noting: the Solana count of non-positive rows (2,765,097) is lower than the Solana `<=$0 or NULL` size bucket (6,162,107). The difference of 3,397,010 rows is the set of Solana transfers with a positive token amount but no usable USD value. On Ethereum the two figures are identical.

---

## Summary

Ethereum and Solana both carry large USDC activity, and almost nothing else about them is alike.

Ethereum is a settlement layer: fewer, much larger transfers, value concentrated in a handful of addresses, and a labelled DEX segment carrying most of the value through one dominant protocol. A group of 12,433 addresses moves nine tenths of it.

Solana is a trading layer: ten times the transfer count at a third of the value, a median transfer in the tens of dollars, a flat protocol landscape, and automation that shows up as frequency rather than as value. When USDC rests between trades there, it rests for under an hour in 99% of cases.

The most honest finding in the dataset is also the least satisfying one: on Solana, 83.09% of the value and on Ethereum 73.91% of the transactions fall into a category that simply has no label. Whatever those addresses are doing, this data cannot say.
