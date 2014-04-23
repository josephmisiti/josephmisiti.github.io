---
layout: post
title:  "Column And Row Sums In Pandas And Numpy" 
date:   2014-4-23
---

I feel like I am constantly looking it up, so now it is documented:

If you want to do a row sum in pandas, given the dataframe df:

```python
df.sum(axis=1)
```

and a column sum:

```python
df.sum(axis=0)
```

If you want to do a row sum in numpy[1], given the matrix X:

```python
import numpy as np
np.sum(X,axis=1)
```

and column sums:

```python
import numpy as np
np.sum(X,axis=0)
```


[1] http://docs.scipy.org/doc/numpy/reference/generated/numpy.sum.html