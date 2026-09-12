---
title: "Conformal selection under covariate shift"
created: 2026-07-17
---
Recall that exchangeability doesn't usually hold in real-world conditions due to the issue of distribution shift — the most common type is covariate shift. Thus the previous framework of conformal p-values will no longer hold.

The setup is the following:
- An independent set of calibration data $\{(X_{i},Y_{i})\}_{i\in\mathcal{D}_{calib}}$ drawn i.i.d from a distribution $P$, where outcomes $Y_i$ are observed. 
- The test samples $\{(X_{j},Y_{j})\}_{j\in\mathcal{D}_{test}}$ are drawn independent and i.i.d from an unknown distribution $Q$, and their outcomes $Y_j$ are unobserved.

The objective is to identify a subset $\mathcal{R}\subseteq\mathcal{D}_{test}$ with outcomes $Y_{j}$ strictly above thresholds $\{c_{n+j}\}_{j \in \mathcal{D}_{\mathbf{test}}}$.

The **covariate shift assumption** is: 
$$
\frac{dQ}{dP}(x,y)=w(x)
$$
where $w(x)$ is a weighting function completely independent from $y.$

An example can be a company which mainly hires candidates with computer science degrees ($x$). However they are now looking to hire candidates with mathematics degrees. 
The distribution has shifted from $P(x)$ to $Q(x)$ however $P(y|x)$ has not changed — $P(y|x)$ is how candidate skills ($x$) translate to job performance ($y$).

 Naturally, we are interested in finding as much $j\in \mathcal{D}_{\text{test}}$ where $Y_{j}>c_{j}$ while also ensuring that the false discovery rate (FDR) is below some specified $q\in(0,1)$.
 
*Define.*
$$
FDR:=\mathbb{E}\left[\frac{\sum_{j\in\mathcal{D}_{test}}1\{j\in\mathcal{R},Y_{j}\le c_{j}\}}{\max\{1,\vert{}\mathcal{R}\vert{}\}}\right]\le q
$$
### Weighted Conformal p-Values
The following setup is similar to the one for [[conformal-prediction/05-conformal-p-values|unweighted conformal p-values.]]

Let $\mathcal{D}_{\text{calib}} = \{1, \dots, n\}$ be the calibration dataset (where both features $X_i$ and outcomes $Y_i$ are observed) and $\mathcal{D}_{\text{test}} = \{n + 1, \dots, n + m\}$ be the test dataset (where features $X_{n+j}$ and selection thresholds $c_{n+j}$ are observed, but responses $Y_{n+j}$ are missing.

*Define.*
A nonconformity score $V(\cdot,\cdot): \mathcal{X} \times \mathcal{Y} \to \mathbb{R}$ is monotone if $V(x,y) \le V(x,y')$ holds for any $x \in \mathcal{X}$ and any $y,y' \in \mathcal{Y}$ obeying $y \le y'$.

The available data is split into two disjoint sets:
1. **Calibration Set ($\mathcal{D}_{\text{calib}} = \{1, \dots, n\}$):** Both covariates $X_i$ and true outcomes $Y_i$ are fully observed. We compute the calibration scores:
    $$
    V_i = V(X_i, Y_i), \quad i = 1, \dots, n
    $$
    
2. **Test Set ($\mathcal{D}_{\text{test}} = \{n + 1, \dots, n + m\}$):** Covariates $X_{n+j}$ and cutoff thresholds $c_{n+j}$ are observed, but the true response $Y_{n+j}$ is unobserved . We compute the hypothesized score at the boundary thresholid:
    $$
    \hat{V}_{n+j} = V(X_{n+j}, c_{n+j}), \quad j = 1, \dots, m
    $$

*Define.* **Weighted Conformal P-Value**
$$
p_j = \frac{\sum_{i=1}^n w(X_i)\mathbf{1}\{V_i < \hat{V}_{n+j}\} + \left(w(X_{n+j}) + \sum_{i=1}^n w(X_i)\mathbf{1}\{V_i = \hat{V}_{n+j}\}\right) \cdot U_j}{\sum_{i=1}^n w(X_i) + w(X_{n+j})}
$$
where $U_j \stackrel{\text{i.i.d.}}{\sim} \text{Unif}([0, 1])$ which is used for tie-breaking.

As expected when weights are $w(x) \equiv 1$ for all $x$, the formula becomes:
$$
p_j = \frac{\sum_{i=1}^n \mathbf{1}\{V_i < \hat{V}_{n+j}\} + \left(1 + \sum_{i=1}^n \mathbf{1}\{V_i = \hat{V}_{n+j}\}\right) \cdot U_j}{n + 1}
$$
which is identical to previous formula for unweighted conformal p-values.

**Lemma:** The following inequality holds if the covariate shift holds and the score function $V$ is monotone:

$$
\mathbb{P}(p_j \le t, \, Y_j \le c_j) \le t, \quad \forall t \in [0, 1]
$$
where the probability is taken jointly over the calibration data $\mathcal{D}_{\text{calib}}$ and the test sample $(X_j, Y_j, c_j)$.

*proof to be done later*

*Define.* **PRDS** 
A random vector $\mathbf{X} = (X_1, \dots, X_m)$ is PRDS on a subset $I$ if for any $i \in I$ and any increasing set $\mathcal{D} \subseteq \mathbb{R}^m$, the conditional probability:

$$
\mathbb{P}(\mathbf{X} \in \mathcal{D} \mid X_i = x) \text{ is non-decreasing in } x
$$
A set $\mathcal{D} \subseteq \mathbb{R}^m$ is increasing if $a \in \mathcal{D}$ and $b \succeq a$ implies $b \in \mathcal{D}$ (where $b \succeq a$ means $b_k \ge a_k$ for every coordinate $k$). 

Controlling the FDR with the BH-procedure requires p-values to PRDS which holds in the unweighted case of p-values.

Informally:
- In the unweighted case, all calibration points carry equal mass $\frac{1}{n+1}$. Thus directly from the formula, a small p-value $p_{1}$indicates that the calibration scores have large values relative to $\hat{V}_{n+1}$. These same large calibration scores $\{V_i\}$ are shared when calculating $p_2, \dots, p_m$, the other test p-values are also pulled downward . This mutual positive correlation ensures PRDS.
- In the weighted case, a p-value $p_{1}$ may be small because either the calibration scores are large or the calibration weights $w(X_i)$ for calibration units with $V_i < \hat{V}_{n+1}$ are small. This can inflate the other p-values which means the joint distribution is not longer positively dependent which violates PRDS.
*proof saved for later.*

*insert weighted conformal algorithm and idea of auxiliary p-values*
*theorem later that shows that loss of PRDS doesn't matter*