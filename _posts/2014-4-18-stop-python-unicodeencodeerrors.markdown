---
layout: post
title:  "Fixing Python UnicodeEncodeErrors" 
date:   2014-4-19
---

If you ever hit this error when trying to process HTML:

```python
UnicodeEncodeError: 'ascii' codec can't encode character u'\xa0' in position 7520: ordinal not in range(128)
```

You can add the following line

```python
thestring.encode('ascii', 'ignore')
```
