---
layout: post
title:  "Stop Python Warnings"
date:   2014-4-14
---

I am not exactly sure what is going on with my Django 1.6 upgrade but I am getting a few weird warnings like this one

```python
josephmisiti$ ./run_kmeans_sparse.py --input data/input_vectors --num_clusters 3
/Users/josephmisiti/mathandpencil/projects/machinelearning/lib/python2.7/site-packages/pytz/__init__.py:29: UserWarning: Module argparse was already imported from /usr/local/Cellar/python/2.7.4/Frameworks/Python.framework/Versions/2.7/lib/python2.7/argparse.pyc, but /Users/josephmisiti/mathandpencil/projects/machinelearning/lib/python2.7/site-packages is being added to sys.path
```

Here is the fix: Add the following to your bashrc file

    export PYTHONWARNINGS="ignore"