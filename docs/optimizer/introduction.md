---
title: Introduction
---

## What is Optimizer?

Optimizer constructs out of an existing portfolio an optimal portfolio. For this, an algorithm is configured which will suggest a list of orders to be placed with the broker. These orders bring the portfolio in line with the optimal portfolio.

The default algorithm underpinning the Optimizer is called **iVaR**.

### What is iVaR?

Traditional investment risk measures that are still used today, such as volatility, have historically been chosen because of their simple mathematical properties. They do not work well to describe the risk of real investment portfolios and are not consistent with what end investors perceive as risk.

Following the above observations, we developed a new, innovative risk measure which we call InvestSuite Value at Risk (iVaR). Our basic premise is that any instrument or portfolio providing strict monotonic growth (i.e., no losses) should be risk-less, regardless of the speed or consistency of the growth. The reason for this premise is that it matches the behaviour of a savings account, which also increases monotonically in value over time, and is considered risk-less by end investors. InvestSuite has developed iVaR (InvestSuite Value at Risk) as a human-centric measure of risk. It is a 4th-generation risk measure that addresses the shortcomings of traditional risk measures, with a clear and distinct focus on controlling risk - over maximising return. Next to iVaR, all contemporary risk measures such as volatility, Value-at-Risk and conditional Value-at-Risk are supported.

<!-- TODO probably to reword a bit @Maarten
 -->
