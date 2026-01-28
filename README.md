# Integration of Demand Projection Framework into UEVF Marginal Valuation

[![Module](https://img.shields.io/badge/UEVF-Demand_Integration-techblue)]()
[![Status](https://img.shields.io/badge/Status-Technical_Addendum-blue)]()
[![License](https://img.shields.io/badge/License-Proprietary-lightgray)]()

This repository contains the mathematical formulation and algorithmic implementation for the **UEVF Demand Integration Module**.

This work serves as the "Demand-Side Bridge" between the *UEVF Demand Projection Framework* (Technical Report 00) and the *Refining MRV* valuation engine. It replaces static load forecasts with stochastic, sector-specific growth models to properly price the risk of demand volatility in reliability markets.

## 🎯 Strategic Objective

Traditional resource adequacy models treat load forecasts as deterministic inputs. This module treats demand as a stochastic variable with sector-specific volatility ($\sigma_{s,z}$), proving that **volatile load growth increases the Marginal Reliability Value (MRV) of capacity.**

This framework enables:
1.  **Dynamic PRM Targeting:** Calibrating reserve margins ($PRM^\star$) based on the economic intersection of $MRV$ and $MC$, adjusted for load uncertainty.
2.  **Sector-Aware Valuation:** Distinguishing between stable residential load and high-variance hyperscale (data center) load.
3.  **Integrated Queue Scoring:** Prioritizing interconnection requests in zones where demand growth is outpacing deliverability.

##  Core Methodology

### 1. Stochastic Demand Projection
We model load $L$ by sector $s$, zone $z$, and time $t$, incorporating stochastic deviation $\epsilon$:
$$L_{s,z,t}(\omega) = L_{s,z,t_0}^{\text{base}} \cdot (1 + g_{s,z})^{(t-t_0)} \cdot (1 + \epsilon_{s,z}(\omega))$$

### 2. The Modified MRV Signal
The Marginal Reliability Value is derived by integrating Expected Unserved Energy ($EUE$) over the demand uncertainty:
$$MRV_{i,z} = VOLL_z \cdot \left( - \frac{\partial EUE_z}{\partial C_i} \right)$$

### 3. The Control Loop (PRM Calibration)
The optimal Planning Reserve Margin is found by minimizing the spread between reliability value and capacity cost:
$$PRM^\star \in \text{argmin} \left| MRV_z(PRM) - MC_{\text{cap},z} \right|$$

## Algorithmic Recipe

The end-to-end implementation (detailed in **Section 4 / Appendix A.11** of the whitepaper) follows this procedure:

1.  **Generate Scenarios:** Create Monte Carlo traces for sectoral load growth.
2.  **Simulate Stack:** Apply thermal FORs, renewable profiles, and transmission deliverability.
3.  **Compute EUE:** Calculate base reliability metrics.
4.  **Derive MRV:** Use finite-difference methods (with Common Random Numbers) to find the marginal value of capacity.
5.  **Calibrate:** Solve for the $PRM$ where Value = Cost.
6.  **Screen Projects:** Score transmission and generation projects based on their ability to reduce the specific $\Delta EUE$ driven by that demand growth.

## Worked Example (Synthetic)
The repository includes a synthetic test case (Zone z) referenced in the whitepaper:

Parameter,Value,Description
Growth (μ),"1.5% (Res), 0.5% (Ind)",Sectoral drift
Volatility (σ),0.3%,Annual stochastic deviation
Base MRV,$4.6M / MW-yr,Value at current PRM
High-Growth MRV,**$6.0M / MW-yr**,Value under +0.5% growth shock
Economic PRM$^\star$,19.3%,Optimal reserve margin


## Citation
Candler, J. & Uriel. (2026). Integration of Demand Projection Framework into UEVF Marginal Valuation: Endogenizing Load Growth Uncertainty in Reliability Pricing. Nous Enterprises LLC.
