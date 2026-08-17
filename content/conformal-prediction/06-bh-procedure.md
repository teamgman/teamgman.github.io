---
title: "Benjamini-Hochberg Procedure"
aliases:
  - 06 ; Conformal Selection ; Benjamini-Hochberg Procedure
created: 2026-07-08
draft: true
---
The following algorithm uses conformal p-values.
![[conformal-prediction/attachments/06-bh-procedure-1.png|618]]
![[conformal-prediction/attachments/06-bh-procedure-2.png|582]] 

Intuitively, as the rank grows, both the threshold and p-value grows, thus selecting in a fair/adaptive way. The Bonferroni correction sets a flat cutoff for every p-value which is overly conservative. In essence, we are comparing the p-value to what we would expect from pure noise in the new procedure.

*might do proofs for BH procedure later*

![[conformal-prediction/attachments/06-bh-procedure-3.png|432]]

Everything in green will be added to our shortlist.

In addition, controlling the FDR requires p-values to be either independent or to possess **Positive Regression Dependency on Subsets (PRDS).**

*Define.*
**PRDS**: A random vector $X = (X_1, \dots, X_m)$ is PRDS on a subset $\mathcal{I}$ if for any $i \in \mathcal{I}$ and any increasing set $D$, the probability $\mathbb{P}(X \in D \mid X_i = x)$ is increasing in $x$.

*proofs saved for later.*

