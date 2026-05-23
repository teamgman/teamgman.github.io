---
title: 01 ; Finite-sample Validity of Distribution-free Prediction Intervals
aliases:
  - 01 ; Finite-sample Validity of Distribution-free Prediction Intervals
created: 2026-05-09
---
Given $(Y_1, \dots, Y_{n+1}) \sim P$ i.i.d., the sequence is **exchangeable**. The rank of $Y_{n+1}$ in the combined set $\{Y_1, \dots, Y_{n+1}\}$ is a discrete uniform random variable:
$$\mathbb{P}(\text{Rank}(Y_{n+1}) = i) = \frac{1}{n+1}, \quad \forall i \in \{1, \dots, n+1\}$$
We want to solve for $k$ such that:
$$\mathbb{P}\left(Y_{n+1} \le \tilde{Y}_{(k)}\right) \ge 1 - \alpha$$
(Where $\tilde{Y}_{(k)}$ denotes the $k$-th order statistic of the combined set $\mathcal{D}_{n+1}$.)

Since each slot is worth $\frac{1}{n+1}$, if you include $k$ slots, your total coverage probability is exactly:
$$\frac{k}{n+1}$$
To satisfy your safety constraint, you set up the inequality:
$$\frac{k}{n+1} \ge 1 - \alpha$$
Solve for $k$:
$$k \ge (1 - \alpha)(n + 1)$$
We then take the ceiling as $k$ must be an integer giving us: 
$$k=\lceil (1-\alpha)(n+1) \rceil$$
The issue is that *it is not computable from the first n points.*
Our goal is to now show: $Y_{n+1} \le \tilde{Y}_{(k)} \iff Y_{n+1} \le Y_{(k)}$ which will make things computable.
![[conformal-prediction/attachments/01-finite-sample-coverage.png]]
$$\implies\mathbb{P}(Y_{n+1} \le \tilde{Y}_{(k)}) = \mathbb{P}(Y_{n+1} \le Y_{(k)})$$
If $\alpha < 1/(n+1)$, then $k = n+1$ as we only accounted for the $k=n$ case.
The $n+1$ smallest is out of bounds in $D_{n}$ thus define $\hat{q}_{n}=+\infty$ which gives us $100\%$ coverage.

To compute this, the $k$-th historical order statistic $Y_{(k)}$ is formally defined via the empirical quantile function of the training sample *which has has not been proved yet.*
$$\hat{q}_n = Y_{(k)}=\text{Quantile}\left(\frac{\lceil(1 - \alpha)(n + 1)\rceil}{n}; \frac{1}{n} \sum_{i=1}^{n} \delta_{Y_i}\right)$$
---
If we also assume there are no ties, we obtain:
$$\mathbb{P}(Y_{n+1} \le \hat{q}_n) \in \left[1-\alpha, \, 1-\alpha + \frac{1}{n+1}\right)$$
![[conformal-prediction/attachments/01-finite-sample-coverage-1.png]]

---
**Current Status:** Verified that $\mathbb{P}(Y_{n+1} \le \tilde{Y}_{(k)}) = \mathbb{P}(Y_{n+1} \le Y_{(k)})$ for all $k \le n+1$.
**Next Objective:** Derive the mechanics of the empirical distribution function (ECDF) to prove that this specific quantile formulation maps bijectively back to the sorted index $Y_{(k)}$.