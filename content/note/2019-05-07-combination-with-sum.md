---
title: "Combinations with sum"
date: 2019-05-07T15:39:30+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["python", "sudoku"]
categories: ["dev"]
draft: false
---

To find all the possible combination for a given sector in a killer sudoku or in a "between 1 and 9" sudoku.

Find all combinations for a between 1 and 9 that sums to 18 with 3 cells

```shell
from itertools import combinations
print [c for c in combinations([2,3,4,5,6,7,8], 3) if sum(c) == 18]
```

Find all combinations in a killer for a sum of 22 in 3 cells

```shell
from itertools import combinations
print [c for c in combinations([1,2,3,4,5,6,7,8,9], 3) if sum(c) == 22]
```
