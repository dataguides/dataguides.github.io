---
layout: post
title: Simple Linear Regression with R
description: "A simple example of performing simple linear regression with R"
category: articles
tags: [r,linear regression,statistics]
---

<section id="table-of-contents" class="toc">
  <header>
    <h3>Contents</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

**This article is a work in progress.**
{: .notice}

## Introduction and Data

This example demonstrates how to perform simple linear regression using R.  The data used in this demonstration is housed in the ISwR package [cite author of text?] and is titled “kfm”.

Here is the complete data set, in CSV format. [link to data.csv]

**insert the data and descriptions here**

**The Task**: perform a linear regression using dl.milk as explanatory and weight as response.

### Getting Started in R

{% highlight r %}
#You may need to install the following packages

install.packages("ISwR")
install.packages("car")
install.packages("MASS")

#Load the ISwR package into R.  This can also
#be done by clicking "Packages" and selecting
#the data set

library(ISwR)
library(car)
library(MASS)

#Attach the data set

attach(kfm)
{% endhighlight %}

### Explore the data: Plotting

One of the most important first steps in any data analysis is to plot the data.  The following code can be used to create a scatterplot in R.  [Note: When using multiple lines of R, it may be best to write a script rather than the command prompt.  For more information on writing and calling a script, click here.]

{% highlight r %}
#Create a Scatterplot of the data

plot(dl.milk,weight,main="Linear Regression of Weight on Breastfeeding",
xlab="Breast Milk Intake (dl/day)",
ylab="Weight (kg)",
pch=19)
{% endhighlight %} 

The scatterplot shows evidence of a positive linear trend, indicating that as the amount of daily breast milk intake increases, weight tends to increase.  The red line is the least squares regression line, which will be discussed in greater detail in just a moment.  The blue line is the lowess (locally weighted scatterplot smoothing) line, which also tries to fit a line to the scatterplot.  If this line is not relatively straight, it may indicate that simple linear regression (without any sort of transformation of variables) is not appropriate.  In this case, the lowess appears relatively straight.  In fact, it appears to be very close to the least square regression line.  All of this indicates that simple linear regression may be appropriate.


### Explore the data: Correlation

{% highlight r %}
#find correlation
cor(dl.milk,weight)
{% endhighlight %}

## Build the Model

This is how you build the model and add a regression line.

{% highlight r %}
#Add the regression line and a lowess
model<-lm(weight~dl.milk)
abline(model,col="red")
lines(lowess(dl.milk,weight),col="blue")
{% endhighlight %}

Simple linear regression
{% highlight r %}
#perform simple linear regression

summary(model)
confint(model,level=.95)
anova(model)
{% endhighlight %}

## Verifying Assumptions

When performing linear regression, we have several assumptions to check:

**Linearity**: There is a linear relationship between the independent and dependent variables. (The mean of the Y values is accurately modeled by a linear function of the X values)
{: .notice}

**Normality of the Errors:**: The random error term is assumed to have a normal distribution with a mean of zero.
{: .notice}

**Homoscedasticity**: The random error term is assumed to have a constant variance.
{: .notice}

**Independence of the Errors**: The errors are independent of each other.
{: .notice}

**No Perfect Multicollinearity**: There is no **perfect** collinearity between independent variables (for multiple linear regression).
{: .notice}
<br />
Let's look at these one-by-one.

#### Linearity of the Relationship

It is necessary to assume that the mean of the response is a linear combination of the predictor variables.  This can be demonstrated visually through the use of a component and residual plot.

{% highlight r %}
#Evaluating Nonlinearity
crPlots(model)
ceresPlots(model)
{% endhighlight %}

The Component+Residual Plot highlights the linearity of the mean.  Therefore, this condition is met.


#### Normality of the Residuals

It is important to assume that the residuals of the regression line are normally distributed.  Though this assumption can often be rejected using a visual approach (in the case of residuals that are clearly non-normal), it is more appropriate to use a formal test for normality.  The code below plots a Q-Q Plot and histogram, as well as performing a Shapiro-Wilk test for Normality.

{% highlight r %}
#Evaluating assumption of Normality of Residuals

qqPlot(model, main="QQ Plot")
dev.new()
sresid <- studres(model) 
hist(sresid, freq=FALSE, 
     main="Distribution of Studentized Residuals")
xfit<-seq(min(sresid),max(sresid),length=40) 
yfit<-dnorm(xfit) 
lines(xfit, yfit)
shapiro.test(sresid)
{% endhighlight %}

There is no reason to doubt the Normality of the residuals, as the Q-Q Plot appears linear and the histogram is relatively Normal.  Furthermore, the Shapiro-Wilk Test for Normality (Null Hypothesis is that data are from a Normally distributed population) provides a w-statistic of .9775.  This corresponds to a p-value of .4533, which is not sufficient to reject the hypothesis of Normality.  Therefore, it is concluded that the residuals are Normally distributed.

#### Homoscedasticity

It is also important to verify that the variance of errors is relatively constant across all levels of the independent variable.  The Breusch-Pagan Test of Non-Constant Error Variance is one way of verifying this assumption.  If the condition is not met, a Spread-Level Plot can be used to determine an appropriate transformation.

{% highlight r %}
#Evaluating assumption of Homoskedasticity
ncvTest(model)
spreadLevelPlot(model)
{% endhighlight %}

The Breusch-Pagan Test provides a very small chi-square statistic (.1806 at df=1).  This corresponds to a p-value of .67, which is not significant to reject the null hypothesis of constant variance.  The Spread-Level Plot suggests a cubic transformation of the independent variable, though this result is meaningless due to the results of the Breusch-Pagan Test.

#### Independence of the Error Terms

{% highlight r %}
#Evaluating Independence
durbinWatsonTest(model)
{% endhighlight %}

#### No Perfect Multicollinearity

{% highlight r %}
#Evaluating Multicollinearity (For models of two or more terms)
vif(model) 
sqrt(vif(model)) > 2 # problem?
{% endhighlight %}

### Model Diagnostics

#### Look for Influential Points

{% highlight r %}
#Influential Points
avPlots(model)
cutoff <- 4/(nrow(mtcars)-length(model$coefficients)-2) 
plot(model, which=4, cook.levels=cutoff)
influencePlot(model, id.method="noteworthy", main="Influence Plot", sub="Circle size is proportial to Cook's Distance")

{% endhighlight %}

#### Extrapolation

This is how you extrapolate 

{% highlight r %}
#extrapolate
predmilk<-seq(0,10,by=.2)
predweight<-(3.5873+predmilk*.2307)
pred<-cbind(predmilk,predweight)
pred
{% endhighlight %}
