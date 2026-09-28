# Project: Insurance Risk & Claim Pattern Analysis
# Author: Samuel Kimathi Irungu
# Description: Collective risk modeling (Frequency * Severity) and pure premium 
#              derivation using Monte Carlo simulation.

# 1. Load Required Libraries
suppressPackageStartupMessages({
  library(ggplot2)
  library(dplyr)
  library(scales)
})

set.seed(42) # Ensure reproducible simulation results

# 2. Portfolio Parameters
n_policies   <- 10000        # Portfolio size (simulated vehicle cohort)
lambda_freq  <- 0.12         # Expected claims per policy/year (Poisson)
gamma_shape  <- 2.5          # Severity distribution shape parameter
gamma_rate   <- 0.0005       # Severity distribution rate parameter (Mean = 5,000)

# 3. Simulate Claim Frequency (Poisson Distribution)
claims_count <- rpois(n = n_policies, lambda = lambda_freq)

# 4. Simulate Claim Severity (Gamma Distribution) & Aggregate Losses
simulated_portfolio <- data.frame(
  PolicyID = 1:n_policies,
  ClaimCount = claims_count
)

# Compute aggregate loss per policyholder
simulated_portfolio$AggregateLoss <- sapply(simulated_portfolio$ClaimCount, function(n) {
  if (n == 0) {
    return(0)
  } else {
    # Generate random claim amounts for n occurrences
    claims <- rgamma(n = n, shape = gamma_shape, rate = gamma_rate)
    return(sum(claims))
  }
})

# 5. Actuarial Metrics Calculation
pure_premium <- mean(simulated_portfolio$AggregateLoss)
var_95       <- quantile(simulated_portfolio$AggregateLoss, 0.95)
var_99       <- quantile(simulated_portfolio$AggregateLoss, 0.99)

cat("--- ACTUARIAL PORTFOLIO SUMMARY ---\n")
cat("Expected Pure Premium: KES", round(pure_premium, 2), "\n")
cat("95% Value at Risk (VaR): KES", round(var_95, 2), "\n")
cat("99% Value at Risk (VaR): KES", round(var_99, 2), "\n")

# 6. Aggregate Loss Distribution Visualization
loss_plot <- ggplot(subset(simulated_portfolio, AggregateLoss > 0), aes(x = AggregateLoss)) +
  geom_histogram(bins = 40, fill = "#1F4E79", color = "white", alpha = 0.85) +
  geom_vline(aes(xintercept = pure_premium), color = "red", linetype = "dashed", linewidth = 1) +
  geom_vline(aes(xintercept = var_95), color = "darkorange", linetype = "dotted", linewidth = 1) +
  annotate("text", x = pure_premium * 1.5, y = 200, label = paste("Pure Premium:\nKES", round(pure_premium)), color = "red") +
  annotate("text", x = var_95 * 1.15, y = 100, label = paste("95% VaR:\nKES", round(var_95)), color = "darkorange") +
  labs(
    title = "Aggregate Claim Severity Distribution (Excluding Zero Claims)",
    subtitle = "Monte Carlo Simulation (10,000 Policies) with Pure Premium & 95% VaR Thresholds",
    x = "Aggregate Claim Amount (KES)",
    y = "Number of Policies"
  ) +
  theme_minimal()

print(loss_plot) 
