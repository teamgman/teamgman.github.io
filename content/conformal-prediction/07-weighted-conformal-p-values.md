---
title: 07 ; Conformal selection under covariate shift
created: 2026-07-17
---
Recall that exchangeability doesn't usually hold in real-world conditions due to the issue of distribution shift — the most common type is covariate shift. Thus the previous framework of conformal p-values will no longer hold.

The setup is the following:
- An independent set of calibration data $\{(X_{i},Y_{i})\}_{i\in\mathcal{D}_{calib}}$ drawn i.i.d from a distribution $P$, where outcomes $Y_i$ are observed. 
- The test samples $\{(X_{j},Y_{j})\}_{j\in\mathcal{D}_{test}}$ are drawn independent and i.i.d from an unknown distribution $Q$, and their outcomes $Y_j$ are unobserved.

The objective is to identify a subset $\mathcal{R}\subseteq\mathcal{D}_{test}$ with outcomes $Y_{j}$ strictly above thresholds $\{c_{j}\}_{j\in \mathcal{D}_{\text{calib}}}$.

The **covariate shift assumption** is: 
$$\frac{dQ}{dP}(x,y)=w(x)$$
where $w(x)$ is a weighting function completely independent from $y.$

An example can be a company which mainly hires candidates with computer science degrees ($x$). However they are now looking to hire candidates with mathematics degrees. 
The distribution has shifted from $P(x)$ to $Q(x)$ however $P(y|x)$ has not changed — $P(y|x)$ is how candidate skills ($x$) translate to job performance ($y$).

 Naturally, we are interested in finding as much $j\in \mathcal{D}_{\text{test}}$ where $Y_{j}>c_{j}$ while also ensuring that the false discovery rate (FDR) is below some specified $q\in(0,1)$.
 
*Define.*
$$FDR:=\mathbb{E}\left[\frac{\sum_{j\in\mathcal{D}_{test}}1\{j\in\mathcal{R},Y_{j}\le c_{j}\}}{\max\{1,\vert{}\mathcal{R}\vert{}\}}\right]\le q$$
The following setup is similar to the one for [[conformal-prediction/05-conformal-p-values|unweighted conformal p-values.]].

Let $\mathcal{D}_{\text{calib}} = \{1, \dots, n\}$ be the calibration dataset (where both features $X_i$ and outcomes $Y_i$ are observed) and $\mathcal{D}_{\text{test}} = \{n + 1, \dots, n + m\}$ be the test dataset (where features $X_{n+j}$ and selection thresholds $c_{n+j}$ are observed, but responses $Y_{n+j}$ are missing.

*Define.*
A nonconformity score $V(\cdot,\cdot): \mathcal{X} \times \mathcal{Y} \to \mathbb{R}$ is monotone if $V(x,y) \le V(x,y')$ holds for any $x \in \mathcal{X}$ and any $y,y' \in \mathcal{Y}$ obeying $y \le y'$.

![[conformal-prediction/attachments/05-conformal-p-values-1.png]]

