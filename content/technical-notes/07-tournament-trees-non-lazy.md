---
title: 07; DSA; Non-lazy deletion for tournament trees.
created: 2026-02-28
aliases:
  - 07; DSA; Non-lazy deletion for tournament trees.
---

> [!NOTE]
> p[i] represents a pointer to the winner.
> Update(i, k) updates the tree to adjust to new key k at leaf node i.

```lua
ALGORITHM LazyDeleteMin(k,n)
	winner = p[1] // the key of the winner
	min_val = key[winner]
	Update(winner, +inf) // triggers logic to remove the winner
	return min_val
```

The challenge is to implement a non-lazy delete algorithm which still runs in $O(\log n)$. Idea is instead to replace the node with the last leaf in the tree. Count represents current number of items while n represents structural offset.
```lua
ALGORITHM DeleteMin(k, n)
	winner_idx = p[1]
	min_val = key[winner_idx]
	last_leaf_idx = n + count -1
	if winner_idx != last_leaf_idx:
		key[winner_idx] = key[last_leaf_idx]
		Update(winner_idx, key[winner_idx])
		
	key[last_leaf_idx] = +inf
	Update(last_leaf_idx, +inf)
	count = count - 1
	return min_val
```

We don't actually physically delete the node but by decreasing count, we will not access it and by performing update it will remove all pointers to the old last leaf.

**Further Reading:**
- [Tournament Trees](https://www.geeksforgeeks.org/dsa/tournament-tree-and-binary-heap/)