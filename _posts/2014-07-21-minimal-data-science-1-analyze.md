---
layout: post
title: 'Minimal Data Science #1: Classify StarCraft 2 players with Python Pandas and
  Scikit-learn.'
date: '2014-07-21T06:56:00.001-07:00'
author: Dat Le
tags: 
modified_time: '2014-07-29T08:52:42.374-07:00'
thumbnail: http://3.bp.blogspot.com/-c57m4J8cSQc/U80-Gyo19WI/AAAAAAAADJE/AUJrG6zWp9U/s72-c/Screen+Shot+2014-07-21+at+21.47.47.png
blogger_id: tag:blogger.com,1999:blog-3261104696526323937.post-3295306444867294091
blogger_orig_url: http://lenguyenthedat.blogspot.com/2014/07/minimal-data-science-1-analyze.html
---

##Forewords##
This is the first in a data science blog series that I'm writing. My goal for this series is not only sharing, tutorializing, but also, making personal n
otes while learning and working as a Data Scientist. I'm looking forward to receiving any feedback from you.

**Note:** The source codes as well as original datasets for this series will also be updated at this [Github repository](https://github.com/lenguyenthedat/minimal-datascience">Github repository) of mine.
{: .notice}

##Preparation##
For the purpose of this project, I'm going to use this [SkillCraft](http://archive.ics.uci.edu/ml/datasets/SkillCraft1+Master+Table+Dataset) data set. (You can also download as well as have a quick look of it from this [ShareCSV url](http://www.sharecsv.com/s/dd4eadbc6e0632dc820b0c017d82aa80/SkillCraft1_Dataset.csv) - Many thanks to [Ken Tran](https://github.com/kentran) and [Huy Nguyen](https://github.com/nvquanghuy) for such a neat tool!)

- **Input**: StarCraft 2 dataset (CSV) with 20 different attributes.

- **Output**: A classification / prediction model to determine League Index (more information and context can be found [here](http://wiki.teamliquid.net/starcraft2/Battle.net_Leagues)

- **Prerequisites**: Python (with [Pandas](http://pandas.pydata.org) and [Scikit-learn](http://scikit-learn.org)), [iPython Notebook](http://ipython.org/notebook.html) (as an awesome IDE), Unix Shell Script.

##In Action##

**Step 1**: Clean the data. I'm going to use a simple Bash script for this.

* As you can see: there is some "missing-value" entries - denoted as "?":

{% highlight bash %}
$ cat SkillCraft1_Dataset.csv | grep "?" | head -2
1064,5,17,20,"?",94.4724,0.0038460052,0.0007827297,3,"9.66332959684589e-06",0.0001352866,0.0044741216,50.5455,54.9287,3.0972,31,0.0007634,7,0.0001062966,0.00011596
5255,5,18,"?","?",122.247,0.0063568492,0.0004328068,3,"1.35252109932915e-05",0.000256979,0.0030431725,30.8929,62.2933,5.3822,23,0.001055,5,0,0.00033813
{% endhighlight %}

* A 1-liner bash script to clean them up:

{% highlight bash %}
$ sed '/?/d' SkillCraft1_Dataset.csv > SkillCraft1_Dataset_clean.csv
{% endhighlight %}

**Step 2**: Load and prepare the dataset. We will use iPython Notebook from now.

* Load:

{% highlight python %}
import pandas as pd
df = pd.read_csv('./Dataset/Starcraft/SkillCraft1_Dataset_clean.csv')
{% endhighlight %}

* Divide the given data set into Train (75%) and Test (25%) sets:

{% highlight python %}
df['is_train'] = np.random.uniform(0, 1, len(df)) <= .75
train_df, test_df = df[df['is_train']==True], df[df['is_train']==False]
{% endhighlight %}

    
**Step 3**: Apply Machine Learning algorithm.

* We will use [Random Forest](http://en.wikipedia.org/wiki/Random_forest) classification for this example:

{% highlight python %}
from sklearn.ensemble import RandomForestClassifier
{% endhighlight %}

* Chose the features set:

{% highlight python %}
features = ["APM","Age","TotalHours","UniqueHotkeys", "SelectByHotkeys", "AssignToHotkeys", "WorkersMade","ComplexAbilitiesUsed","MinimapAttacks","MinimapRightClicks"]
{% endhighlight %}

* Define your classifier and train it:
(n_estimators is the number of trees in your forest, in this case I would just use 10.)
    
{% highlight python %}
rfc = RandomForestClassifier(n_estimators=10)
rfc.fit(train_df[list(features)], train_df.LeagueIndex)
{% endhighlight %}


**Step 4:** Evaluations:

* [Contingency Table](http://en.wikipedia.org/wiki/Contingency_table):

{% highlight python %}
pd.crosstab(test_df.LeagueIndex, rfc.predict(test_df[features]), rownames=["Pred"], colnames=["Actual"])
{% endhighlight %}

{% highlight python %}
RandomForestClassifier
Actual   1   2   3   4   5   6  7
Pred                             
1       17  18   7   4   0   0  0
2       14  22  20  19   7   1  0
3        8  28  33  64  18   4  0
4        9  14  41  86  61  17  0
5        0   6  16  58  66  45  0
6        0   0   3  25  47  73  0
7        0   0   0   0   3   5  1
{% endhighlight %}

We can see that the correctness reduce greatly at League #1 and #7. It's simply because they are the 2 classes with lowest number of members.
With a little over 3000 entries in our given dataset, we simply don't have enough data to "learn".
{: .notice}


* Plot it!

{% highlight python %}
pd.crosstab(test_df.LeagueIndex, rfc.predict(test_df[features]), rownames=["Pred"], colnames=["Actual"]).plot()
{% endhighlight %}

![image](http://1.bp.blogspot.com/-sqr28hLbjts/U80Z7iiC2_I/AAAAAAAADIs/TZwtf5du418/s1600/Screen+Shot+2014-07-21+at+21.47.09.png)

* [RMSD](http://en.wikipedia.org/wiki/Root-mean-square_deviation):

{% highlight python %}
np.sqrt(sum(pow(test_df.LeagueIndex - classifier.predict(test_df[features]),2)) / float(len(test_df)))
{% endhighlight %}

{% highlight python %}
>1.23877447987
{% endhighlight %}

##Conclusions##
- With the above (relatively confident) result, we can already see the benefit of using data science in a simple classification problem.
- There are, of course, a few ways to improve our result:
Larger dataset. 3000 entries is simply not enough for this model.
Better dataset. We would love to get a dataset with an even distribution of League members.
For Random Forest, simply improving our features set in quality and quantity, or increasing the number of trees would give us a better result at a certain performance cost.
Consider different classifiers (described below).


##Take it to another level##
Now that we've used a proper classification technique (Random Forest), but that's not everything about data science. Choosing the right technique for the right task is not only an interesting problem, but also mandatory.
In this part, we are going to compare between various classifiers and pick the best one for the task:

- Mass-define classifiers:

{% highlight python %}
from sklearn.ensemble import ExtraTreesClassifier, RandomForestClassifier
from sklearn.neighbors import KNeighborsClassifier
from sklearn.lda import LDA
from sklearn.qda import QDA
from sklearn.naive_bayes import GaussianNB
from sklearn.tree import DecisionTreeClassifier

classifiers = [ 
    ExtraTreesClassifier(n_estimators=10),
    RandomForestClassifier(n_estimators=10),
    KNeighborsClassifier(100),
    LDA(),
    QDA(),
    GaussianNB(),
    DecisionTreeClassifier()
]
{% endhighlight %}

- Train them:

{% highlight python %}
import time
features = ["APM","Age","TotalHours","UniqueHotkeys", "SelectByHotkeys", "AssignToHotkeys",
            "WorkersMade","ComplexAbilitiesUsed","MinimapAttacks","MinimapRightClicks" ]

for classifier in classifiers:
    print classifier.class.name
    start = time.time()
    classifier.fit(train_df[list(features)], train_df.LeagueIndex)
    print "  -> Training time:", time.time() - start
{% endhighlight %}


{% highlight python %}
ExtraTreesClassifier
  -> Training time: 0.0490028858185
RandomForestClassifier
  -> Training time: 0.0870010852814
KNeighborsClassifier
  -> Training time: 0.00781583786011
LDA
  -> Training time: 0.0202190876007
QDA
  -> Training time: 0.00545406341553
GaussianNB
  -> Training time: 0.00332403182983
DecisionTreeClassifier
  -> Training time: 0.0392520427704
{% endhighlight %}

- Evaluation:

{% highlight python %}
for classifier in classifiers:
    print classifier.__class__.__name__
    print np.sqrt(sum(pow(test_df.LeagueIndex - classifier.predict(test_df[features]),2)) / float(len(test_df)))
{% endhighlight %}

{% highlight python %}
ExtraTreesClassifier
1.2280633269
RandomForestClassifier
1.19202778179
KNeighborsClassifier
1.23231678199
LDA
1.1164728514
QDA
1.55181813847
GaussianNB
1.49456379263
DecisionTreeClassifier
1.38093328738
{% endhighlight %}

As we can see, Linear Discriminant Analysis (LDA) clearly won with a relatively low training time and best RMSD!
{: .notice}
