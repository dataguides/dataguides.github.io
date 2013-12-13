---
layout: post
title: Decision Trees with R
description: "A simple example of creating decision trees using R"
category: articles
tags: [R,decision tree,statistics]
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

This example demonstrates how to perform decision tree analysis using R.  The dataset used in this demonstration is titled `beetles`. The variables are:

| **Variable** | **Description** |
| ------------ | --------------- |
| `width` | A numeric vector, maximal width of aedeagus (microns) |
| `angle` | A numeric vector, front angle of aedeagus (1 unit=7.5 degrees) |
| `species` | Species of beetle from genus Chaetocnema (3 levels) |



**Data as CSV:** [Here is the complete data set, in CSV format.]({{ site.url }}/files/beetles.csv)
{: .notice}


**The Task**: Using only the provided measurements of a beetle’s aedeagus, determine the species of beetle from the genus *Chaetocnoma*.  The three species are *concinna* (Con), *heikertingeri* (Hei), and *heptapotamica* (Hep).

### Getting Started in R

To begin, you need to have R installed on your computer. You can download it at the [R Project website](http://www.r-project.org/). We also recommend the [R Studio](http://www.rstudio.com/). Launch R and then type the following commands at the command line.

{% highlight r %}
#You may need to install the following packages

install.packages("party")

#Load the package into R.  This can also
#be done by clicking "Packages" and selecting
#the data set

library(party)

#Read the csv into R

beetles=read.csv("beetles.csv", header=TRUE)
{% endhighlight %}

### Partitioning the Data

Since the goal of this procedure is to predict a categorical variable based on numeric inputs, a decision tree may be appropriate here.  

First, the data must be separated into training and validation sets.  A common percentage for these datasets is 70% training and 30% validation.  The training dataset will be used to build the decision tree, while the validation set will be used to test the predictions and evaluate the model.

{% highlight r %}
set.seed(1234)
train<-sample(2,nrow(beetles),replace=TRUE,prob=c(0.7,0.3))
beetles.tr<-beetles[train==1,]
beetles.va<-beetles[train==2,]
{% endhighlight %}


### Building the Decision Tree

Now that the data are separated, the decision tree may be created.  The commands below create a decision tree with the default settings.

{% highlight r %}
model<-Species~Width+Angle
beetles_tree<-ctree(model,data=beetles.tr)
table(predict(beetles_tree),beetles.tr$Species)
{% endhighlight %}

![Confusion Matrix]({{ site.url }}/images/r_dt_01.png)

The output shows that the tree possesses clear predictive power, as the species are almost completely predicted correctly.  Printing the tree reveals the decision rules and properties.

{% highlight r %}
print(beetles_tree)
{% endhighlight %}

![Decision Rules]({{ site.url }}/images/r_dt_02.png)

The first split was on aedeagus width of 133 microns.  Both of these divisions were then split again on Angle (one at 12, and the other at 13).  The weights output reveals how many observations are present in a particular leaf.  To get more detailed information, the tree may be plotted.

{% highlight r %}
plot(beetles_tree)
{% endhighlight %}

![Decision Tree]({{ site.url }}/images/r_dt_03.png)

All of the information is still available from the print function, but additional insights are provided.  The p-values are available at each split, as well as bar graphs of the species divisions within leaves.  Notice that it is not possible to get exact percentages of species within leaves.  For example, Node 3 contains approximately 75% *heikertingeri*.  For a similar chart with percentages, the type can be specified as "simple".

{% highlight r %}
plot(beetles_tree, type="simple")
{% endhighlight %}

![Decision Tree Simple]({{ site.url }}/images/r_dt_04.png)

This chart reveals that the proportion of *heikertingeri* in Node 3 is, in fact, 75%.

## Validation of the Model

Now, the model must be tested against the validation data.  This allows one to see how the model responds to new data, and serves as an assessment of the model’s validity if implemented.

{% highlight r %}
beetles_treetest<-predict(beetles_tree, newdata=beetles.va)
table(beetles_treetest, beetles.va$Species)
{% endhighlight %}

![Validate Confusion Matrix]({{ site.url }}/images/r_dt_05.png)

The model seems to perform well on the validation set, correctly predicting all but one specimen. 

## Conclusion

We hope you found this guide to decision trees in R useful!