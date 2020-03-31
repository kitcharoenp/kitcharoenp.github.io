---
layout: post
title: "sklearn : missing values"
published: true
categories: [sklearn]
---

### [Univariate feature imputation][1]
```python
>>> import numpy as np
>>> from sklearn.impute import SimpleImputer
>>> imp = SimpleImputer(missing_values=np.nan, strategy='mean')
>>> imp.fit([[1, 2], [np.nan, 3], [7, 6]])
SimpleImputer()
>>> X = [[np.nan, 2], [6, np.nan], [7, 6]]
>>> print(imp.transform(X))
```

[1]: https://scikit-learn.org/stable/modules/impute.html#univariate-feature-imputation "Univariate feature imputation"
