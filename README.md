# quantitative-solana-tokens-analysis
Quantitative Solana Tokens Analysis is a collection of data-driven research files exploring trading strategies after token migrations from Pump.fun to Raydium. Each Excel model applies statistical methods to test profitability, entry conditions, and liquidity behavior in different market contexts.

# Files Overview

- Immediate Post-Migration Sniping – Analysis of early entry opportunities right after migration. The model examines the short-term inefficiency between Pump.fun’s bonding curve and Raydium’s initial liquidity pool, where early selling is often mathematically unfavorable for the deployer.

- Dip-Buy Strategy After Migration – Focused on delayed entry during local dips post-migration, evaluating risk-adjusted returns and volatility stabilization before liquidity expands.

- US Market Hour Dip-Buy Strategy – Similar to Strategy 2 but optimized for U.S. trading hours (EST), capturing behavioral and liquidity patterns unique to the American session.

# Immediate Post-Migration Sniping

Idea & observation

The idea came from watching bundle launches: the deployer (“dev”) pulls ~80% of the token supply on Pump.fun, which triggers an immediate migration to Raydium.

On-chain inspection showed that of 85 SOL collected, only 79 SOL are actually deposited into the Raydium pool, while the token amount changes (due to bonding curve → LP mechanics).

From AMM V2 math (x * y = k) it follows that if the dev attempts to sell all their tokens immediately after migration, they would incur a significant loss (~22 SOL).

Implication: the deployer’s break-even price in the new pool is approximately +63% above the migration price. Since deployers are typically rational and unlikely to dump at a loss, a predictable entry window opens for early buyers.

Tactic

The strategy is a targeted snipe immediately after migration - enter right after migration and before any selling pressure pushes price toward break-even.

I implemented a real-time script that alerts on bundle migrations(look for it here: https://github.com/web3romank/pump-fun-bundle-launch-alert-bot). Measured reaction window: 1 minute 42 seconds from bundle detection to full migration — the time available to prepare and execute the snipe.

Main operational risk: pushing the price toward break-even with your own buy; hence calculating optimal entry size is critical.

Calculations & optimization

The optimal entry sizing model combines AMM V2 math, projected volume of traders who will snipe before me, and empirical volume/price distributions from historical migrations.

Detailed calculations live in pfsnipe max entry.xlsx: liquidity curve modeling, simulation of sequential snipes, and a parameter sweep to find entry sizes that avoid pushing price to break-even.

Optimization result (rounded to integers): recommended snipe size - 6 SOL. This minimizes the chance of pushing price to the deployer’s break-even under typical competitor volume.

Statistics & forecast

Entry sizes and risk metrics were derived via statistical fitting across multiple formulae and scenarios (see the parameter sheets in the workbook).

Based on average migration frequency and historical returns, I projected an expected daily profit ≈ 9.097 SOL/day. Methodology and assumptions for the forecast are documented in the file.

Limitations & assumptions

The deployer rationality assumption (no intentional selling at a loss) is central; if a deployer behaves irrationally, the edge disappears.

Results are sensitive to the estimated number of competing snipers and execution latency.

Models are calibrated to historical bundle parameters (e.g., 80% supply triggers). Different parameters require re-calibration.

# Dip-Buy Strategy After Migration
That refers to the pf bundle dip buy.xlsx file

In some time Immediate Post-Migration Sniping bacame not that profitable as a lot of traders knew about that inefficiency. So I developed another strategie that don't imply sniping. 

Overview
This workbook contains a statistical, matrix-style analysis of post-migration dip-buy opportunities for Solana tokens migrated from Pump.fun to Raydium. Historical migrations were collected over an extended period, extrapolated to a longer horizon, and used to compute realized profits depending on where you buy (market-cap at entry) and where you sell (market-cap at exit).

Data & construction

Historical migration events were aggregated into a single dataset and extrapolated to produce long-run statistics.

From these data, a two-dimensional summary table (profit matrix) was produced: rows represent entry market-cap buckets (vertical axis, left — e.g., from ~140k down to ~15k), columns represent exit market-cap buckets (horizontal axis, top — e.g., from ~145k up to very large caps).

Each matrix cell contains the empirical profit (absolute or percent) associated with buying at the row’s MC bucket and selling at the column’s MC bucket, computed from historical samples that match those MC ranges.

How to read the matrix

Locate the entry MC on the left (row) and the target sell MC on top (column). The intersecting cell is the historical profit for that buy/sell pair.

Cells highlighted in yellow denote profitable scenarios based on historical outcomes; white cells are historically losing scenarios.

Many of the high-profit cells (especially on the right-hand side of the matrix) show larger profits but are based on a small sample size — they are visually bright but statistically weak/insignificant.

What the workbook shows (interpretation)

There is a clear region of MC pairs where buying on the dip after migration historically produced positive returns — this gives a rule-of-thumb: if you buy near X MC and exit near Y MC, you historically captured Z% profit.

The matrix acts as a look-up profitability map: it converts observed/target MCs into expected profit using precomputed empirical outcomes. That enables rule-based dip buys (e.g., “buy when MC ≤ A and plan to sell when MC ≥ B”).

Because the right-most profitable cells rely on few observations, they may be outlier-driven (one or two big winners) — these require careful statistical vetting before being treated as real edges.

Statistical notes & robustness

The matrix is an aggregated / discretized estimator: it simplifies continuous MC dynamics into buckets. This makes interpretation straightforward, but introduces bucket-edge issues and varying sample sizes across cells.

Important to accompany each cell with sample counts, standard errors, and confidence intervals — without these, heat colours can mislead. The workbook already marks high-profit/low-sample areas visually; adding numeric counts will make the risk explicit.

The extrapolation to longer periods assumes stationarity of migration behavior and market microstructure — a major caveat: if launch mechanics change, the matrix must be re-calibrated.

Operational considerations

Execution costs (fees, slippage), latency, and front-running risk are not visible in a raw profit matrix; they must be subtracted or simulated to obtain net results.

Recommended entry sizing should consider effect on price (your buy may itself move price into a different MC bucket).

Use rolling-window re-calibration to detect regime shifts (e.g., older migrations vs recent ones).

Limitations / caveats (summary)

Cells with large historical profit but low sample size are statistically unreliable.

Survivorship / selection biases in the migration sample can inflate apparent edges.

Real-world execution (latency, MEV, fees) reduces realized profit vs the matrix numbers.

# US Market Hour Dip-Buy Strategy

The overall idea is the same as in previous one but the data was sorted for US-trading time migrations suspecting that time will be more profitable.
