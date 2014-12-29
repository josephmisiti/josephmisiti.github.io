---
layout: post
title:  "Random R commands everyone should know"
date:   2014-12-06
---

I hate R with a passion! With the invention of iPython + Statsmodels + Pandas, or even Julia these days, it is becoming painfully obvious that it is the worst option of them all. The syntax sucks, there are 10 ways to do the same thing, and it isn't scalable. Unfortunately, if you work in data science, it is still unavoidable a lot of statistical work is still done in R.

I dont think you need to learn the language fluently, but here are a series of commands that definitely will help you get by:

#### Install A Package
{% highlight r %}
install.packages("ggplot2")
{% endhighlight %}

#### Changing your working directory

{% highlight r %}
setwd("path/to/working/directory")
{% endhighlight %}

#### Run an R script

{% highlight r %}
source("path/to/script.R")
{% endhighlight %}

#### Open plot window from within R script

{% highlight r %}
dev.new()
bp <- boxplot(log(posts$all.count+1) ~ posts$post.hour,
col="lightblue",
xaxt="n",
pch=19,
xlab="Hour Of Day [0 is 12AM]",
ylab="log(Likes + Shares + Comments)",
main="Total Number Of Post Interactions vs Hour Of Day")
{% endhighlight %}

#### Reading data from a CSV

{% highlight r %}
posts <- read.csv( '/path/to/file.csv', header=TRUE, strip.white=TRUE)
{% endhighlight %}

#### Converting a Data Frame Column From String to Date

{% highlight r %}
df$date <- as.Date(df$date,format="%y/%m/%d")
{% endhighlight %}


#### Calculate the sum of multiple columns in a Data Frame

{% highlight r %}
df$avg.interaction <- apply(df[,c(3,4,5)],1,sum,na.rm=TRUE)
{% endhighlight %}

#### Calculate the mean of multiple columns in a Data Frame

{% highlight r %}
df$avg.interaction <- apply(df[,c(3,4,5)],1,mean,na.rm=TRUE)
{% endhighlight %}

#### Filtering A Data Frame Column On Upper + Lower Bounds

{% highlight r %}
df$filtered <- df[posts$values<=upper_bound & posts$value>=lower_bound,]
{% endhighlight %}

#### String Concatenation

{% highlight r %}
paste("Hello", " World")
{% endhighlight %}

#### Dump the Results of a Linear Regression

{% highlight r %}
print(summary(lm(y ~ x),data=dataset)))
{% endhighlight %}

#### Number of Rows In A Dataframe

{% highlight r %}
NROW(data)
{% endhighlight %}

#### Number of Columns In A Dataframe

{% highlight r %}
NCOL(data)
{% endhighlight %}

#### Names of dataframe columns

{% highlight r %}
names(data)
{% endhighlight %}

#### List all variables in the workspace

{% highlight r %}
ls()
{% endhighlight %}




