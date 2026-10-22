# Portfolio analysis of stock market indices with NumPy

A small project in which I analyze a portfolio split across four stock market indices using only NumPy, without pandas. The goal was to put what I learned about NumPy to the test on real, slightly messy data instead of hand-made examples.

## The question

A portfolio is split like this: 40% S&P 500, 25% Nasdaq Composite, 20% Dow Jones, 15% Nikkei 225. I want to know:

- how much each index returned and how much risk it took over the last years;
- how much they move together;
- what the portfolio's worst loss from its peak was;
- what can be expected, within a realistic range, one year from now.

## What the notebook does

1. Downloads the four daily series from FRED and loads them with `np.genfromtxt` into structured arrays, with dates as `datetime64`.
2. Removes missing days and aligns the dates of the four series (the Nikkei follows the Japanese calendar) with `np.intersect1d` and `np.isin`, checking that the alignment is correct.
3. Computes returns, annualized volatility, percentiles, the worst day of each index and a ranking by return/volatility ratio.
4. Computes the correlation matrix by hand, with standardization and a matrix product, and checks it against `np.corrcoef`.
5. Builds the portfolio with `@`, its value over time with `np.cumprod` and its drawdown with `np.maximum.accumulate`.
6. Simulates 10,000 possible years with a reproducible Monte Carlo bootstrap (`np.random.default_rng` with a fixed seed).
7. Plots the portfolio value, the drawdown and the distribution of the simulated outcomes with matplotlib.
8. Saves the results to an `.npz` file and checks that they reload identically.

Everything is vectorized: the only loops run over the four indices, never over the data.

## Main results

Data downloaded on 21 October 2026, about ten years of history. Re-running the notebook on another day changes the numbers slightly.

- The Nasdaq has the best return/volatility ratio (0.76), the Dow Jones the worst (0.63).
- The US indices are highly correlated with each other (above 0.81), the Nikkei much less (about 0.18).
- The capital grew 3.97 times, with a maximum drawdown of 32.5% on 23 March 2020.
- One year ahead, according to the simulation: median value 1.16 times the capital, 0.88 in the pessimistic scenario (5th percentile), 1.53 in the optimistic one (95th percentile). Probability of ending at a loss: 19.4%.

## Limitations

- Tokyo closes before New York opens: aligning by date puts on the same row closing prices many hours apart, so the Nikkei's correlation with the US indices is probably underestimated.
- The simulation resamples a decade that was very favorable for stock markets, so the results are probably optimistic.
- The Nikkei is in yen and the other indices in dollars: the portfolio does not convert currencies, and for a euro-based investor currency risk would also matter.
- Constant weights assume daily rebalancing with no costs.

This is not investment advice: it is a data analysis exercise.

## How to run it

The easiest way is to open the notebook in Google Colab and run all cells: the data is downloaded automatically. Locally you need Python 3, `numpy`, `matplotlib` and `requests`.

## Data

Source: FRED, Federal Reserve Bank of St. Louis (series SP500, NASDAQCOM, DJIA, NIKKEI225). The series belong to their respective index providers, so they are not included in the repository: the notebook downloads them each time.
