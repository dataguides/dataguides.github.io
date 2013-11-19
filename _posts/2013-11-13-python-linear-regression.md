---
layout: post
title: Simple Linear Regression with Python
description: "A simple example of performing linear regression with python"
category: articles
tags: [python,linear regression,statistics]
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

## Introduction and Data

This brief tutorial shows an example of how to perform linear regression with python. There are many ways to perform linear regression with python. This example uses the [SciPy](http://www.scipy.org/) library that is commonly used in python-based analysis.
<br /><br />

The data set that we use for this example is [Cricket Chirps Vs. Temperature](http://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), from *The Song of Insects*, by Dr. G.W. Pierce, Harvard College Press.

Here is the complete data set, in CSV format. For this exercise, copy/paste this data into a blank file and save it as `crickets.csv`.

| Var | Description |
| :--------| :-----------|
| `chirps` | Chirps per second for the striped ground cricket |
|====
| `temp` | Temperature in degrees Fahrenheit |
{: rules="groups"}

{% highlight text %}
chirps,temp
20,88.59999847
16,71.59999847
19.79999924,93.30000305
18.39999962,84.30000305
17.10000038,80.59999847
15.5,75.19999695
14.69999981,69.69999695
17.10000038,82
15.39999962,69.40000153
16.20000076,83.30000305
15,79.59999847
17.20000076,82.59999847
16,80.59999847
17,83.5
14.39999962,76.30000305
{% endhighlight %}

### Explore the data: plotting

One of the first steps in exploratory data analysis always is to plot the data. Let's look at how to plot the data with matplotlib in python. The code is commented. See the *Additional References* section at the bottom of this post for links to more resources about plotting data in python.

{% highlight python %}
# Import the numpy library
import numpy

# Import the plotting library and refer to it as plt
import matplotlib.pyplot as plt

# Use numpy to load the data into a 2D numpy array
data = numpy.loadtxt("crickets.csv",delimiter=",",skiprows=1)

# Slice the numpy array to get the data vectors
# 0 represents the first column of data and 
# 1 represents the second column of data
chirps = data[:,0]
temps  = data[:,1]

# Chirps are the dependent variable here, so we will plot 
# them on the y-axis

# Establish with matplot lib that we're creating a figure
fig = plt.figure()

# Add the main title for the figure
fig.suptitle('Temperature (f) vs. Cricket Chirps per Second', fontsize=12)

# Establish the plot as part of the figure
theplot = fig.add_subplot(1,1,1)

# Set that it is a scatter plot with temps as x and chirps as y
theplot.scatter(temps,chirps)

# Set the axis labels
theplot.set_xlabel('Temperature (f)')
theplot.set_ylabel('Chirps per Second')

# Display the plot
plt.show()
{% endhighlight %}

And here is the resulting plot... <br /><br />

![Exploratory plotting]({{ site.url }}/images/py_slr_fig1.png)

Hey, those look pretty correlated... let's check it out. 

### Explore the data: Correlation

It's easiest to include all of the relevant exploratory data tests in a single script. That way, you can run one script and analyze your output to better understand your data. 

For the purposes of this tutorial, I'm breaking the components out and will present the comprehensive script at the end. 

To check for correlation with Pearsons correlation coefficient, you can either write another script or just use `ipython`, either as a notebook, QT console, or in Powershell. Let's go with Powershell.

Launch Powershell and navigate to the directory where your `crickets.csv` file lives. Type `python` at the prompt and then enter this code:

{% highlight python %}
>>> import numpy
>>> from scipy import stats
>>> data = numpy.loadtxt("crickets.csv",delimiter=",",skiprows=1)
>>> chirps = data[:,0]
>>> temps  = data[:,1]
>>> stats.pearsonr(temps,chirps)
(0.83514378703115499, 0.00010667185499560227)
{% endhighlight %}

According to the [SciPy reference guide](http://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.pearsonr.html), the `stats.pearsonr(x,y)` function "Calculates a Pearson correlation coefficient and the p-value for testing non-correlation." Note that this is a 2-tailed p-value.

So, in our results above, our correlation coefficient is 0.835, indicating strong correlation between temps and chirps. The p-value for the test of non-correlation is 0.0001, so we reject the null hypothesis that the values are not correlated and conclude that they are correlated.

## Checking Assumptions

When performing linear regression, we have several assumptions to check:

There is a linear relationship between the independent and dependent variables. (The mean of the Y values is accurately modeled by a linear function of the X values)
{: .notice}

2. The random error term is assumed to have a normal distribution with a mean of zero.
3. The random error term is assumed to have a constant variance (also called homoscedasticity)
4. The errors are independent
5. There is no **perfect** collinearity between independent variables (for multiple linear regression)

Let's look at these one-by-one.

#### Linearity of the Relationship

There are several ways to test this assumption. We've already done two of them! 

The first is to create a scatterplot of the data and to eyeball it to determine whether the relationship looks linear. As you see in the graph above, it certainly does.

The second is to calculate the correlation between them. As we saw above, there is a strong positive correlation between temperature and the chirps per second of the crickets. 

### Additional References

**Plotting with Matplotlib**

- [2D and 3D plotting in Python](http://nbviewer.ipython.org/urls/raw.github.com/jrjohansson/scientific-python-lectures/master/Lecture-4-Matplotlib.ipynb)
- [Adding text to matplotlib plots](http://matplotlib.org/users/text_intro.html)