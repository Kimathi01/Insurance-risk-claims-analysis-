# General Insurance Risk & Claim Pattern Analysis

## 1. Project Overview
This project models claim frequency and claim severity for a motor insurance portfolio using statistical loss distributions in R. The goal is to estimate baseline pure premiums and quantify exposure to large-loss tail events using actuarial loss modeling techniques.

## 2. Actuarial Methodology
The portfolio risk is separated into two components:
- **Claim Frequency ($N$):** Modeled using a **Poisson distribution** ($\lambda = 0.12$ annual frequency per vehicle).
- **Claim Severity ($X$):** Modeled using a **Gamma distribution** (shape = 2.5, rate = 0.0005) to capture positive skewness and long right tails typical in motor property damage.
- **Aggregate Loss ($S$):** Calculated as the compound distribution:
  $$S = \sum_{i=1}^{N} X_i$$

## 3. Key Findings & Business Insights
- **Pure Premium Benchmark:** The baseline pure premium (expected loss per policyholder) is estimated at **KES 600.00**.
- **Value at Risk (VaR 95%):** Simulated 95th percentile aggregate annual claim per risk is **KES 1,940.00**, indicating the necessary capital buffer above pure premium to ensure solvency.
- **Underwriting Implication:** Portfolios with young driver loadings require an additional 25% frequency multiplier to avoid loss ratio deterioration.

## 4. Repository Structure
- `claims_risk_model.R`: Script containing data simulation, distribution fitting, Monte Carlo aggregate loss engine, and visualization.
- `output/`: Generated plots for severity distributions and aggregate loss distributions.

## 5. Tools & Packages
- **Language:** R
- **Packages:** `ggplot2`, `dplyr`, `scales`
