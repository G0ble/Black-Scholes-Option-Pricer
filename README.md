# Black-Scholes Option Pricer — Greeks & Monte Carlo Validation
 
Pricing European options two independent ways — the closed-form **Black-Scholes** solution and **Monte Carlo** simulation — computing the full set of **Greeks** (Δ, Γ, Vega, Θ, ρ) analytically, and validating every result through put-call parity and cross-method agreement.
 
**Headline result:** the Monte Carlo estimate (10.4673 ± 0.0467) converges to the exact Black-Scholes price (10.4506) within its 95% confidence interval, and all Greeks satisfy the put-call parity identities exactly.
 
---
 
## Why this project
 
Option pricing is the foundation of quantitative finance. This project implements the Black-Scholes framework **from the underlying mathematics** — not by wrapping a library like QuantLib — to demonstrate genuine understanding of where the formulas come from and how to validate them.
 
The emphasis throughout is on **verification**: every number is checked against an independent source of truth. The analytical price is validated by put-call parity; the Greeks are validated by their parity relationships; and the entire closed-form approach is validated against Monte Carlo simulation built on completely different mathematics. Two methods agreeing is far stronger evidence than one method looking plausible.
 
---
 
## What's inside
 
### 1. Closed-form Black-Scholes price
 
European call and put prices implemented directly from:
 
$$C = S_0 N(d_1) - K e^{-rT} N(d_2), \qquad P = K e^{-rT} N(-d_2) - S_0 N(-d_1)$$
 
with a guard for the degenerate expiry case (T → 0 returns intrinsic value). Validated via **put-call parity**: $C - P = S_0 - Ke^{-rT}$, which holds to six decimal places.
 
### 2. The Greeks, analytically
 
All five primary Greeks (Delta, Gamma, Vega, Theta, Rho) derived from their closed forms. Key relationships demonstrated:
 
- **Gamma and Vega are identical for calls and puts** — a consequence of put-call parity being linear in S and independent of σ.
- **Delta_call − Delta_put = 1**, verified numerically.
- The interpretation of each Greek as a partial derivative (Delta and Gamma as the option's "velocity" and "acceleration" with respect to the underlying).
### 3. Monte Carlo pricing with variance reduction
 
Pricing by simulating terminal prices under the risk-neutral measure, using the **exact GBM solution** (no time-discretization error, since only the terminal price matters for European payoffs):
 
$$S_T^{(i)} = S_0 \exp\left[\left(r - \tfrac{1}{2}\sigma^2\right)T + \sigma\sqrt{T}\,Z_i\right]$$
 
Includes **antithetic variates** for variance reduction and reports the **standard error**, so every estimate comes with a confidence interval rather than a bare number.
 
### 4. Convergence & Greek visualizations
 
- **Convergence plot**: Monte Carlo estimate converging to the analytical price as M grows, with the confidence band visibly shrinking as $1/\sqrt{M}$.
- **Variance-reduction plot**: log-log standard-error decay confirming the −1/2 slope, comparing plain vs antithetic sampling.
- **Greek profiles**: all five Greeks plotted across a range of underlying prices, revealing their characteristic shapes (Delta's S-curve, Gamma's and Vega's bells centered near the strike, Theta's trough, Rho's monotone rise).
---
 
## Limitations & next steps
 
This project prices **European options under constant volatility** — the foundational case. Natural extensions:
 
- **American options** via Longstaff-Schwartz least-squares Monte Carlo (early-exercise via regression on continuation value).
- **Implied volatility extraction** — invert Black-Scholes against real market option prices to recover the implied volatility and reconstruct the **volatility smile**.
- **Stochastic volatility models** (Heston, SABR) that reproduce the smile that constant-volatility Black-Scholes cannot.
- **Path-dependent payoffs** (Asian, barrier options) where full path simulation is required and Monte Carlo becomes essential.
The implied-volatility extractor is the most natural immediate follow-up: it turns this pricer into a tool for reading what the market actually expects, which is where options trading begins.
 
