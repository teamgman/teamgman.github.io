---
title: The Convex Hull Problem
created: 2026-01-17
aliases:
  - Merge Hull Algorithm
---
Pre-sorting makes designing the algorithm substantially easier to design however makes the worst case $O(\log n)$ as well while in the MergeHull algorithm below, it may frequently be much faster as it prunes many points.

```lua
Algorithm MergeHull(S):
	if |S| == 1:
		return S
	else:
		Partition L and R based on the median x-coordinate.
		L = MergeHull(L)
		R = MergeHull(R)
		return Merge(L,R)
```

We can view H as a stack which is LIFO.
We can use sentinel nodes to simplify the boundary checking process and increase clarity.
```lua
Algorithm Merge(L, R):
	L.append(+∞, 0)
	R.append(+∞, 0)
	
	H = []
	i = 0
	j = 0
	while i + j < |L| + |R| - 2
		if L[i].x < R[j].x:
			P = L[i]
			i++
		else
			P = R[j]
			j++
		
		while |H| >= 2:
			A = H[-2]
			B = H[-1]
			if CalcAngle(A, B, P) < 180 deg:
				H.remove(B)
			else
				break
		
		H.add(P)
			
	return H
```
For case $n > 2$:
$$
T_{n} = 2T_{\frac{n}{2}}+cn
$$
$$
T_{2}=d
$$
where $c$ and $d$ are constants. Using the master theorem with Case (c), this gives us a total complexity of $\Theta(n\log n)$.
