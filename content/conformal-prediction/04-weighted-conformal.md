---
title: 04 ; Weighted Full and Split Conformal Prediction
aliases:
  - 04 ; Weighted Full and Split Conformal Prediction
created: 2026-06-14
---
For conformal prediction, the guarantee of marginal coverage relies strictly on the assumption that the data is exchangeable, which does not hold in real-world conditions; this is called the covariate shift problem.

### Covariate Shift 
Covariate shift occurs when the marginal distribution of the features changes between the training and test sets, while the conditional distribution of the response given the features remains invariant. Mathematically, this looks like:

- **Training Data:** $(X_i, Y_i) \sim P = P_X \times P_{Y|X}$ for $i = 1, \dots, n$.
    
- **Test Data:** $(X_{n+1}, Y_{n+1}) \sim \tilde{P} = \tilde{P}_X \times P_{Y|X}$.
    
Notice that $P_X \neq \tilde{P}_X$ but $P_{Y|X}$ stays the same.

Empirically, this leads to bad coverage when we use our standard methods.

### Moving Past Exchangeability

In standard conformal prediction, exchangeability ensures that the test score $R_{n+1}$ is equally likely to take any value from the unordered set of observed scores $\{r_1, \dots, r_{n+1}\}$. This uniform probability ($1/(n+1)$) allows us to construct prediction sets using the empirical quantile function.

Under the rank-based quantiles that give us this formula, let $k=\lceil (1-\alpha)(n+1) \rceil$:
$$
\mathbb{P} \left\{ R_{n+1} \le \text{Quantile} \left( \frac{k}{n}; \frac{1}{n} \sum_{i=1}^{n} \delta_{R_i} \right) \right\} \ge 1 - \alpha
$$
With some work we can obtain:
$$
\mathbb{P} \left\{ R_{n+1} \le \text{Quantile} \left( 1-\alpha; \frac{1}{n+1} \sum_{i=1}^{n+1} \delta_{R_i}\right) \right\} \ge 1-\alpha
$$
However, the issue is that $R_{n+1}$ appears on the other side of the equation within the summation, thus we obtain:
$$
\mathbb{P} \left\{ R_{n+1} \le \text{Quantile} \left( 1-\alpha; \frac{1}{n+1} \sum_{i=1}^n \delta_{R_i} + \frac{1}{n+1} \delta_{\infty} \right) \right\} \ge 1-\alpha
$$
*Proof.*
![[conformal-prediction/attachments/04-weighted-01.png]]
This comes from the fact that for a discrete distribution $F$, where we can assign all points greater than $q$ to arbitrary values larger than $q$ which gives us a new distribution $F'$, then $\text{Quantile}(\beta;F)=\text{Quantile}(\beta; F')$.

This can be proven in a different way that allows for more general use.
![[conformal-prediction/attachments/04-weighted-02.png]]
![[conformal-prediction/attachments/04-weighted-03.png]]
However, when under covariate shift, the scores $R_1, \dots, R_{n+1}$ are no longer exchangeable, which requires the development of a new idea.

### Weighted Exchangeability

While standard exchangeability is broken, the random variables under covariate shift satisfy a generalized property known as **weighted exchangeability**.

Because the sequence is weighted exchangeable, the probability that the test variable assumes any specific observed value is no longer uniform. Instead, we define a normalized probability weight, $p_i^w$, for each observation $i = 1, \dots, n+1$.

Conceptually, data points from the training set that are more representative of the test distribution $\tilde{P}_X$ are assigned a higher weight while those that aren't have a lower weight.
![[conformal-prediction/attachments/04-weighted-04.png]]

### Weighted Quantile Lemma
Let $Z_{1}, \dots,Z_{n+1}$ be weighted exchangeable random variables with respect to weight functions $w_{1},\dots,w_{n+1}$. WLOG, assume that these are distinct almost surely. 
Let 
$$
R_{i}=V(Z_{i};Z_{1},\dots,Z_{n+1}),\quad i=1,\dots ,n+1,
$$
where $V$ is an arbitrary non-conformity score function and is symmetric in its last $n+1$ arguments.

*Define.*
![[conformal-prediction/attachments/04-weighted-05.png]]

Finally, for any target error level $\alpha \in (0, 1)$, the coverage probability is guaranteed by:
$$
\mathbb{P} \left\{ R_{n+1} \le \text{Quantile} \left( 1-\alpha; \sum_{i=1}^n p_i^w \delta_{R_i} + p_{n+1}^w \delta_\infty \right) \right\} \ge 1-\alpha
$$
_Proof._
Let $E_z$ denote the event that $\{Z_1,\dots,Z_{n+1}\} = \{z_1,\dots,z_{n+1}\}$, and let $r_i = V(z_i; z_1,\dots,z_{n+1})$, for $i = 1,\dots,n + 1$. For each $i$, we have

$$
\mathbb{P}(R_{n+1} = r_i \mid E_z) = \mathbb{P}(Z_{n+1} = z_i \mid E_z) = \frac{\sum_{\sigma:\sigma(n+1)=i} f(z_{\sigma(1)},\dots,z_{\sigma(n+1)})}{\sum_{\sigma} f(z_{\sigma(1)},\dots,z_{\sigma(n+1)})}
$$

Since $Z_1,\dots,Z_{n+1}$ are weighted exchangeable,
![[conformal-prediction/attachments/04-weighted-06.png]]
this implies that

$$
\mathbb{P}\left\{ R_{n+1} \le \text{Quantile}\left(1 - \alpha; \sum_{i=1}^{n+1} p_i^w(z_1,\dots,z_{n+1})\delta_{r_i}\right) \;\middle|\; E_z \right\} \ge 1 - \alpha.
$$

This is equivalent to

$$
\mathbb{P}\left\{ R_{n+1} \le \text{Quantile}\left(1 - \alpha; \sum_{i=1}^{n+1} p_i^w(Z_1,\dots,Z_{n+1})\delta_{R_i}\right) \;\middle|\; E_z \right\} \ge 1 - \alpha,
$$

Then, by marginalization:

$$
\mathbb{P}\left\{ R_{n+1} \le \text{Quantile}\left(1 - \alpha; \sum_{i=1}^{n+1} p_i^w(Z_1,\dots,Z_{n+1})\delta_{R_i}\right) \right\} \ge 1 - \alpha.
$$

Finally, by the same arguments as before:
$$
\mathbb{P} \left\{ R_{n+1} \le \text{Quantile} \left( 1-\alpha; \sum_{i=1}^n p_i^w \delta_{R_i} + p_{n+1}^w \delta_\infty \right) \right\} \ge 1-\alpha
$$

### Weighted Full Conformal Prediction

This lemma allows us to construct a valid weighted conformal prediction set, $\hat{C}_n^w(x)$.

Recall for full conformal prediction, we generate a hypothetical test value $y$ which is assumed to be our new data point and then calculate all non-conformity scores with this new data point. If $R^{(x,y)}_{n+1}$ falls below the $1-\alpha$ quantile, then it is within our set.

*Theorem.*
Assume that $Z_i = (X_i, Y_i) \in \mathcal{X} \times \mathcal{Y}$, $i = 1, \dots, n + 1$ are weighted exchangeable with weight functions $w_1, \dots, w_{n+1}$. Define a weighted conformal set (based on the first $n$ samples) at a point $x \in \mathcal{X}$, with nominal error level $\alpha \in (0,1)$. Let

$$
R_i^{(x,y)} = V\Big((X_i, Y_i); Z_1, \dots, Z_n, (x,y)\Big), \quad i = 1, \dots, n,
$$
$$
R_{n+1}^{(x,y)} = V\Big((x,y); Z_1, \dots, Z_n, (x,y)\Big),
$$
for an arbitrary score function $V$ that is symmetric in its last $n + 1$ arguments.

![[conformal-prediction/attachments/04-weighted-conformal-07.png]]

However, recall that before when dealing with continuous data like stock prices, it's not really possible to test infinite hypothetical values.
### Weighted Split Conformal Prediction
We divide our data into:
1. **Training Set ($Z_0$):** Used to train a fixed point predictor, $\mu_0(x)$.
2. **Calibration Set:** Keep samples separate to calculate how "wrong" our model typically is.

Because the model $\mu_0$ is already trained and fixed on $Z_0$, the score function simplifies to:
$$
V(x,y) = |y - \mu_0(x)|
$$
$$
\hat{C}_n^w(x) = \mu_0(x) \pm \text{Quantile}\left(1 - \alpha; \sum_{i=1}^n p_i^w(Z_1, \dots, Z_n, (x,y))\delta_{|Y_i-\mu_0(X_i)|} + p_{n+1}^w(Z_1, \dots, Z_n, (x,y))\delta_\infty \right)
$$
which has coverage $1-\alpha$ conditional on $Z_{0}$ by the previous theorem.