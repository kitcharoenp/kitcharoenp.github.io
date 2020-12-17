---
layout: post
title: "Parallel processing"
published: false
categories: [python]
---

### pool.map - multiple arguments

### List of lists
`pool.map` accepts only a list of single parameters as input. Multiple parameters can be passed to pool by a list of parameter-lists, or by setting some parameters constant using partial.

```python
import multiprocessing
import numpy as np

data_pairs = [ [3,5], [4,3], [7,3], [1,6] ]

# define what to do with each data pair ( p=[3,5] ), example: calculate product
def myfunc(p):
    product_of_list = np.prod(p)
    return product_of_list

if __name__ == '__main__':
    pool = multiprocessing.Pool(processes=4)
    result_list = pool.map(myfunc, data_pairs)
    print(result_list)

[15, 12, 21, 6]
```

### using partial()
`partial` can be used to set constant values to all arguments which are not changed during parallel processing, such that only the first argument remains for iterating.

```python
import multiprocessing
from functools import partial

data_list = [1, 2, 3, 4]

def prod_xy(x,y):
    return x * y

def parallel_runs(data_list):
    pool = multiprocessing.Pool(processes=4)
    prod_x=partial(prod_xy, y=10) # prod_x has only one argument x (y is fixed to 10)
    result_list = pool.map(prod_x, data_list)
    print(result_list)

if __name__ == '__main__':
    parallel_runs(data_list)

[10, 20, 30, 40]
```

[1]: https://sites.google.com/site/python3tutorial/multiprocessing_map " Parallel processing"

[2]: https://stackoverflow.com/questions/25553919/passing-multiple-parameters-to-pool-map-function-in-python "Passing multiple parameters to pool.map()"

[3]: https://sites.google.com/site/python3tutorial/multiprocessing_map/multiprocessing_partial_function_multiple_arguments "pool.map - multiple arguments"
