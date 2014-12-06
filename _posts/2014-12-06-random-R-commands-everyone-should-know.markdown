---
layout: post
title:  "Random R commands everyone should know"
date:   2014-12-06
---

I had R with a passion. With the invention of iPython + Statsmodels + Pandas, or even Julia these days, it is becoming painfully obvious that it is the worst option of them all.

#### Changing your working directory

```
setwd("path/to/working/directory")
```

#### Run an R script

```
source("path/to/script.R")
```

#### Open plot window from within R script

```
dev.new()
bp <- boxplot(log(posts$all.count+1) ~ posts$post.hour,
col="lightblue",
xaxt="n",
pch=19,
xlab="Hour Of Day [0 is 12AM]",
ylab="log(Likes + Shares + Comments)",
main="Total Number Of Post Interactions vs Hour Of Day")
```
