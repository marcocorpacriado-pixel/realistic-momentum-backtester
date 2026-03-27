# 🚨 The Momentum Trap: Exposing Data Snooping in Algorithmic Trading

*A hyper-realistic Python backtesting engine demonstrating the hidden costs of quantitative strategies and the dangers of curve-fitting.*

## 📖 The Premise
A common academic trope and YouTube myth suggests that a simple "60-day Momentum Strategy" on the S&P 500 easily outperforms the market. However, theoretical backtests often rely on adjusted closing prices and ignore the harsh realities of live trading. 

This project challenges theoretical momentum strategies by building a **hyper-realistic backtesting engine** from scratch. Instead of calculating theoretical percentage returns, this engine simulates the actual friction of a retail/quant account trading the S&P 500 universe from 2005 to the present.

## ⚙️ The Realistic Engine Features
Unlike standard backtests (which often produce heavily overfitted results), this engine enforces strict, real-world constraints:

* 💰 **Realistic Initial Capital:** Starts with a `$10,000` portfolio, exposing the destructive nature of minimum flat fees on small accounts.
* 🧱 **Whole Shares Only:** Discards fractional share math. If the algorithm cannot afford a full share at the exact execution price, the cash sits idle.
* 💸 **Fixed Broker Commissions:** Applies a `$1.00` minimum commission per trade (mimicking modern brokers like IBKR or Trade Republic).
* 📉 **Slippage Tax:** Enforces a `0.20%` (20 bps) slippage penalty on every single buy and sell order to account for the bid-ask spread.
* 👻 **Survivorship Bias & Liquidity:** Dynamically checks for delistings (M&A, bankruptcies) and forces liquidation. It also enforces a 126-day Average Dollar Volume liquidity filter to ensure we only trade highly liquid assets.
* 🔮 **No Look-Ahead Bias:** Signals are calculated using adjusted prices at the close of day $T$, but execution strictly happens at the *unadjusted* prices of day $T+1$.

## 📊 Methodology & Parameter Sweep
To test the robustness of the momentum anomaly, we ran a parameter sweep evaluating lookback periods ranging from 60 to 240 trading days. The portfolio rebalances monthly, selecting the top 20 assets based on their momentum score.

**Timeframe:** January 2005 - Present
**Benchmark:** S&P 500 ETF (SPY) Buy & Hold

## 📈 Results (2005 - Present)

| Lookback (days) | Final Capital ($) | CAGR (%) | Max Drawdown (%) | Sharpe Ratio | Total Friction ($) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **60** | 37,500.22 | 6.58 | -64.11 | 0.27 | **11,638.55** |
| **90** | 55,207.45 | 8.65 | -60.16 | 0.37 | 9,935.08 |
| **120** | 45,821.39 | 7.71 | -60.54 | 0.33 | 8,096.12 |
| **150** | 38,063.60 | 6.79 | -57.34 | 0.28 | 6,870.67 |
| **180** | 57,503.94 | 9.02 | -62.18 | 0.38 | 6,820.39 |
| **210** | 80,110.82 | 10.91 | -61.53 | 0.46 | 6,522.31 |
| **240** | 42,220.29 | 7.47 | -60.61 | 0.31 | 5,174.28 |
| **SPY (Benchmark)** | **85,426.15** | **10.89** | **-52.90** | **0.53** | **0.00** |

*(Note: Total friction = Commissions + Slippage)*

## 💡 Key Takeaways

1. **Friction is an Account Killer:** The classic 60-day momentum strategy generated over `$11,600` in trading costs. It literally destroyed more money in broker fees and spread than the initial `$10,000` capital purely through high turnover.
2. **The Danger of Data Snooping:** While the 210-day lookback mathematically tied the SPY's return, picking it now is a classic example of curve-fitting. Furthermore, it suffered a significantly worse Max Drawdown (-61.5% vs SPY's -52.9%). 
3. **Passive Investing is Hard to Beat:** None of the momentum variations cleanly beat a simple, zero-friction S&P 500 Buy & Hold strategy on a risk-adjusted basis.

**Conclusion:** If you optimize a parameter in the past without realistic execution costs, your spreadsheet will say you are a millionaire, but the real market will crush you in months. Break your own models before the market does.

## 🛠️ Tech Stack & How to Run
* **Language:** Python
* **Libraries:** `pandas`, `numpy`, `yfinance`, `matplotlib`, `seaborn`

To run the backtest yourself:
1. Clone the repository.
2. Ensure you have your S&P 500 pricing data in `.parquet` format (or adapt the data loader for your own CSVs).
3. Run the script to generate the parameter sweep and charts.
