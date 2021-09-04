---
layout : post
title : "Merge Two Pandas DataFrames on Index / MultiIndex"
categories : [pandas]
published : true
---

### Merge DataFrames Using Merge

```python
df = pd.merge(df1, df2, left_index=True, right_index=True)
```
