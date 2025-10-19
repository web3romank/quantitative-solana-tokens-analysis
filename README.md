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
