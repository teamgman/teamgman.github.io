---
title: 02 ; Equivalence of Finite-Sample Quantile and Counting Predicates for Split Conformal Prediction
aliases:
  - 02 ; Equivalence of Finite-Sample Quantile and Counting Predicates for Split Conformal Prediction
created: 2026-05-11
---

Let's now apply conformal prediction to regression problems.

There is a naive approach where we calculate residuals directly on the training dataset.

However, if you train a machine learning model $f(x)$ on a dataset $D_{\text{train}}$, and then calculate your residuals $R_i = |y_i - f(x_i)|$ using that exact same dataset, exchangeability no longer holds. The model overfits/optimizes on $D_{\text{train}}$, minimizing the training residuals. This breaks exchangeability for $R_{n+1}$ because the test point wasn't part of that optimization profile.
## Split Conformal Prediction
Let $D$ be partitioned into two non-overlapping sets:
- Training Set $D_{1}$: Used to optimize the model parameters to get $f(x)$. 
- Calibration Set $D_{2}$: Consisting of $n_2$ independent points.

Compute calibration residuals:
$$R_i = V(X_i, Y_i) = |Y_i - f(X_i)| \quad \text{for } i \in D_2$$
$$\text{Data Points } (X_i, Y_i) \sim \text{i.i.d.} \implies \text{Residuals } R_i \sim \text{Exchangeable}$$
By sorting the calibration residuals ($R_{(1)} < R_{(2)} < \dots < R_{(n_2)}$) and choosing index $k = \lceil(1-\alpha)(n_2+1)\rceil$, exchangeability guarantees:
$$\mathbb{P}\left( V(X_{n+1}, Y_{n+1}) \le R_{(k)} \right) \ge 1-\alpha$$
The prediction set constructed with this out-of-sample threshold $R_{(k)}$ yields:
$$\hat{C}_n(X_{n+1}) = \left[ f(X_{n+1}) - R_{(k)}, \; f(X_{n+1}) + R_{(k)} \right]$$
The upper bound of the coverage only holds if there are no ties (the no-ties condition):
$$\mathbb{P}\left( Y_{n+1} \in \hat{C}_n(X_{n+1}) \;\middle|\; (X_i, Y_i), i \in D_1 \right) \in \left[ 1 - \alpha, \; 1 - \alpha + \frac{1}{n_2 + 1} \right)$$
---
### Score Functions
We can use any score function as long as it treats data symmetrically. The metric $V(x,y)$ is a **conformity score function** that quantifies how poorly a label $y$ fits an input $x$ given a frozen predictor $\hat{f}_{n_1}$.

A score is **negatively-oriented** if lower values imply a better, more accurate model prediction (e.g., standard absolute residuals $V(x,y) = |y - \hat{f}_{n_1}(x)|$).

The valid prediction set:
$$\hat{C}_n(x) = \left\{ y : V(x,y) \le \hat{q}_{n_2} \right\}$$
Where $\hat{q}_{n_2}$ is the $\lceil(1 - \alpha)(n_2 + 1)\rceil$-th smallest score observed in the calibration set $D_2$.

A score is **positively-oriented** if higher values imply a better match (common in classification settings).

We invert the operator and threshold for the positive case:
$$\hat{C}_n(x) = \left\{ y : V(x,y) \ge Y_{(\lfloor \alpha(n_2 + 1) \rfloor)} \right\}$$
![Positive Orientation Sorting Framework](conformal-prediction/attachments/02-split-conformal-prediction-proof.png)

---
We define the valid prediction set $\hat{C}_n(x)$ with the following three equivalent statements.
1. **The Order Statistic Formulation**
$$\hat{C}_n(x) = \left\{ y : V(x,y) \le \lceil (1 - \alpha)(n_2 + 1) \rceil \text{ smallest of } R_i, \; i \in D_2 \right\}$$
2. **The Empirical Quantile Formulation**
$$\hat{C}_n(x) = \left\{ y : V(x,y) \le \text{Quantile}\left( \frac{\lceil (1 - \alpha)(n_2 + 1) \rceil}{n_2}; \; \frac{1}{n_2} \sum_{i \in D_2} \delta_{R_i} \right) \right\}$$
3. **The Empirical Counting Formulation**
$$\hat{C}_n(x) = \left\{ y : \frac{1}{n_2} \sum_{i \in D_2} \mathbf{1}\{R_i < V(x,y)\} \le \frac{\lceil (1 - \alpha)(n_2 + 1) \rceil}{n_2} \right\}$$
![ECDF and Generalized Inverse Derivation](conformal-prediction/attachments/02-split-conformal-prediction-proof-1.png)
![Mapping Counting Measures back to Order Statistics](conformal-prediction/attachments/02-split-conformal-prediction-proof-2.png)

---
**Current Status:** Formally proved the bijection between the ECDF, the empirical quantile function, and historical order statistics for split conformal prediction.
**Next Objective:** It turns out that split conformal prediction is not good as it has a constant width across all data points which means it may undercover and overcover at the same time.