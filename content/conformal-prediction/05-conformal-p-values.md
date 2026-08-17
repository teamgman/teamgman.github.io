---
title: "Conformal P-Values"
created: 2026-06-28
aliases:
  - 05 ; Conformal Selection ; Conformal P-Values
---
In real-world conditions, an analyst is not looking for an confidence interval for every single candidate but rather a shortlist of candidates from this massive pool; individuals of this pool posses unobserved true outcomes $Y$ that exceed a threshold $c$.

Scenarios where false positives are very costly such as drug discovery or financial fraud,  ensuring the **False Discovery Rate (FDR)** $(\frac{\text{False Positives}}{\text{False Positives + True Positives}})$ is controlled becomes very important.

Naively, one might use conformal prediction to select based on $\hat{C}_{1-\alpha}(X)={1}$. However this is an issue as marginal coverage only guarantees the average across the entire dataset not within our shortlist. 

A solution is the Bonferroni correction which $\hat{C}_{1-\frac{q}{m}}(X_{n+j})={1}$ which upper bounds the FDR below $q$. However the issue, is that the bar is set so high that the model loses its **power** (ability to avoid a Type II error). Thus we require a new solution.

### Random Hypothesis Testing
$$
H_{j}:Y_{n+j}\leq c_{j},\quad j=1,\dots,m
$$
Here $H_{j}$ is our null hypothesis whereas our true outcome is when $Y_{n+j}<c_{j}$which means our candidate is a true discovery. Our set of true nulls $H_{0}$ is defined as $H_{0}=\{j:Y_{n+j}\leq c_{j}\}$. Note that unlike traditional testing around around a fixed hidden population parameter, a unobserved future random variable is used, thus it is called predictive / random hypothesis testing.
### Conformal P-Values
We define a nonconformity score function $V(x,y)$ which outputs how poorly an outcome $y$ conforms to what the model expects at a feature state $x$. The model $\hat{\mu}$ is trained independently of both the calibration and test samples, thus it is fixed and given. For example: $V(x,y)=|y-\hat{\mu}(x)|$ which is the absolute residual.

Now *assume* that we are able to observe $Y_{n+j}$ and thus have $V_{n+j}=V(X_{n+j},Y_{n+j})$. You are also able to calculate the nonconformity scores for all known calibration data: $V_i = V(X_{i}, X_{i})$ for $i = 1, \dots, n$.

*Define.* 
$$
p^*_j = \frac{\sum_{i=1}^n \mathbf{1}\{V_{i} < \hat{V}_{n+j}\} + U_j \cdot \left(1 + \sum_{i=1}^n \mathbf{1}\{V_{i} = \hat{V}_{n+j}\}\right)}{n + 1}
$$
which we can call the **Oracle Conformal P-Value** which requires known the true unobserved test outcome $Y_{n+j}$.

$U_j$ is a random draw from a uniform distribution between 0 and 1 ($U_j \sim \text{Unif}[0,1]$) which resolves the problem when there are scores exactly equal to $V_{n+j}$. This causes us to achieve valid p-values: $p_j^* \sim \text{Unif}[0,1]$. For example, supposed $\alpha=0.05$ but our dataset only allows p-values to be $0.01$ or $0.10$, thus we round up to $0.10$ which causes us to lose statistical power.

Now to resolve the problem of not knowing $Y_{n+j}$, we need to use **monotone** nonconformity score functions.

*Define.*
A nonconformity score $V(\cdot,\cdot): \mathcal{X} \times \mathcal{Y} \to \mathbb{R}$ is monotone if $V(x,y) \le V(x,y')$ holds for any $x \in \mathcal{X}$ and any $y,y' \in \mathcal{Y}$ obeying $y \le y'$.

![[conformal-prediction/attachments/05-conformal-p-values-1.png]]

Instead of evaluating at the test score at the unknown $Y_{n+j}$, we evaluate at threshold $c_{j}$.

*Define.*

$$p_j = \frac{\sum_{i=1}^n \mathbf{1}\{V_{i} < \hat{V}_{n+j}\} + U_j \cdot \left(1 + \sum_{i=1}^n \mathbf{1}\{V_{i} = \hat{V}_{n+j}\}\right)}{n + 1}$$
where $\hat{V}_{n+j}=V(X_{n+j},c_{j})$

This is called the **observable conformal p-value.**

The monotonicity is important because knowing $V_{n+j}\leq \hat{V}_{n+j}$. When we have a larger score, it will be larger then more calibration points, thus the numerator grows. Thus naturally $p^*_{j}\leq p_j\text{ on the null event } \{j\in H_{0}\}$.

An inflated p-value is conservative and thus when we swap $p^*_{j}$ for observable $p+j$, the number of false discoveries is non-increasing.

Intuitively, it means we are at least $1-p_{j}$ confident that $Y_{n+j}>c_{j}$ and $p_{j}$ quantifies how extreme the threshold $c_{j}$ is to the usual behaviour of outcomes.

Now, we need to process these conformal p-values to translate individual confidence scores to a shortlist of candidates.