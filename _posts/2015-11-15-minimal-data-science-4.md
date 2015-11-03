---
layout: post
title: 'Minimal Data Science #4: Winning a Data Science challenge'
date: '2015-11-03T20:50:00.001-07:00'
author: Dat Le
tags: code, data science, machine learning, tutorial, aws, python, pandas, scikit learn, kaggle, dextra, XGBoost
modified_time: '2015-11-03T20:50:00.001-07:00'
comments: true
---

![image](http://i.imgur.com/J2dm605.png)

# Introduction
This is the fourth post (and quite possibly the last) of my **Minimal Data Science** blog series, the previous posts can be located here:

- [lenguyenthedat.com/minimal-data-science-1-starcraft](http://lenguyenthedat.com/minimal-data-science-1-starcraft/)
- [lenguyenthedat.com/minimal-data-science-2-avazu](http://lenguyenthedat.com/minimal-data-science-2-avazu)
- [lenguyenthedat.com/minimal-data-science-3-mnist-neuralnet](http://lenguyenthedat.github.io/minimal-data-science-3-mnist-neuralnet/)

<br>
In this post, we will be looking into a recent Data Science challenge hosted in Singapore by [Dextra](http://www.dextra.sg/) in August 2015: the [MINDEF Data Analytics Challenge](http://www.dextra.sg/challenges/ministry-of-defence-data-analytics-challenge/).

<br>
The task is to predict resignation probabilities within the military for 8000 personnel, given a training set of 15000 personnel with data such as demographic, work, and family-related information.

**Note:** The source codes as well as original datasets for this series will also be updated at this [Github repository](https://github.com/lenguyenthedat/minimal-datascience) of mine.
{: .notice}

# Choosing my model
Normally, for such a data set, tree-based models work surprisingly well. After running a quick test with various models, [Random Forrest](https://en.wikipedia.org/wiki/Random_forest) and [Gradient Boosting Machine](https://en.wikipedia.org/wiki/Gradient_boosting) just stood out completely.
<br><br>
![image](http://i.imgur.com/2ktAPv7.png)
<br><br>
The implementation of such algorithm is also playing a significant role. I chose [dmlc's XGBoost](https://github.com/dmlc/XGBoost) since it is by far one of the best implementations of Gradient Boosting Machine, and it has been the winner for most of the challenges lately.

# Feature engineering
The very first step of feature engineering is to figure out roughly how important the original features are:

{% highlight python %}
from matplotlib import pylab as plt

outfile = open('xgb.fmap', 'w')
i = 0
for feat in features:
  outfile.write('{0}\t{1}\tq\n'.format(i, feat))
  i = i + 1
outfile.close()
importance = classifier.get_fscore(fmap='xgb.fmap')
importance = sorted(importance.items(), key=operator.itemgetter(1))
df = pd.DataFrame(importance, columns=['feature', 'fscore'])
df['fscore'] = df['fscore'] / df['fscore'].sum()

# Plotitup
plt.figure()
df.plot()
df.plot(kind='barh', x='feature', y='fscore', legend=False, figsize=(25, 15))
plt.title('XGBoost Feature Importance')
plt.xlabel('relative importance')
plt.gcf().savefig('Feature_Importance_xgb.png')
{% endhighlight %}

![image](http://i.imgur.com/x118QjQ.png)
*Full set of original feature (with name censored) importances as provided by XGBoost*
<br><br>
Below is the list of feature engineering tasks that were done for the data set:

- Combining `promotion` and `gender`:

{% highlight python %}
data['promo1_gender'] = data['PROMO_LAST_1_YR' ].map(str) + data['GENDER']
data['promo2_gender'] = data['PROMO_LAST_2_YRS'].map(str) + data['GENDER']
data['promo3_gender'] = data['PROMO_LAST_3_YRS'].map(str) + data['GENDER']
data['promo4_gender'] = data['PROMO_LAST_4_YRS'].map(str) + data['GENDER']
data['promo5_gender'] = data['PROMO_LAST_5_YRS'].map(str) + data['GENDER']
{% endhighlight %}

- Combining `age` and `gender`:

{% highlight python %}
data['age_gender'] = data['GENDER'].map(str) + data['AGE_GROUPING']
{% endhighlight %}

- Splitting `rank_grouping` - this feature is actually a combination of level and type of employment:

{% highlight python %}
data['Rank_1'] =
  data['RANK_GROUPING'].apply(lambda x: x.split(' ')[0])
data['Rank_2'] =
  data['RANK_GROUPING'].apply(lambda x: x.split(' ')[1] if len(x.split(' ')) > 1 else '')
{% endhighlight %}

- Capping `salary_increment` - for this particular data set, some might have more than 10x or even 100x increment in salary, which indicates either noises or out-liners:

{% highlight python %}
# Salary increment. Max to set = 101. It doesn't matter anyone getting more than this or not.
train['TOT_PERC_INC_LAST_1_YR'] =
  train['TOT_PERC_INC_LAST_1_YR'].apply(lambda x: 101 if x > 101 else x)
train['BAS_PERC_INC_LAST_1_YR'] =
  train['BAS_PERC_INC_LAST_1_YR'].apply(lambda x: 101 if x > 101 else x)
{% endhighlight %}

- Fixing `min_child_age` - originally if there is no kid, `min_child_age` was set as 0, which is a wrong value to be used. We can't use `null`, `mean()`, or `mode()` for this value either, and should instead consider this age to be much higher than for those actually has kids.

{% highlight python %}
def mca(row):
  if row['NO_OF_KIDS'] == 0:
    return '35'
  else:
    return row['MIN_CHILD_AGE']
train['MIN_CHILD_AGE'] = train.apply(mca,axis=1)
test['MIN_CHILD_AGE'] = test.apply(mca,axis=1)
{% endhighlight %}

- Removing noisy features.

{% highlight python %}
noisy_features = [`cherry-picked list based on local performance and feature importances`]
features = [c for c in features if c not in noisy_features]
features_non_numeric = [c for c in features_non_numeric if c not in noisy_features]
{% endhighlight %}

- Dealing with `null` data. I simply used `mean()` for numerical features and `mode()` for categorical features

{% highlight python %}
def fit(self, X, y=None):
  self.fill = pd.Series([X[c].value_counts().index[0] # mode
    if X[c].dtype == np.dtype('O') else X[c].mean() for c in X], # mean
    index=X.columns)
  return self
{% endhighlight %}

- `NATIONAL SERVICEMEN` always get classified as resigned - this is actually more of a "hard-coded" fact than feature engineering.

{% highlight python %}
if test[test[myid] == i]['EMPLOYEE_GROUP'].item() == 2:
  predictions += [[i,1]]
{% endhighlight %}

# Parameter tuning
Choosing the correct parameters is essential to building strong models. In regard to tuning XGBoost, there is not much to say except following the guides in [XGBoost's param_tuning.md notes](https://github.com/dmlc/xgboost/blob/master/doc/param_tuning.md).
<br><br>
As for my best single model, below was the parameters I used:

{% highlight python %}
params = {'max_depth':6, 'eta':0.01, 'silent':1,
          'objective':'multi:softprob', 'num_class':2, 'eval_metric':'mlogloss',
          'min_child_weight':3, 'subsample':1,'colsample_bytree':0.55, 'nthread':4}
num_rounds = 990
{% endhighlight %}

# Cross-Validation for local testing
Most data science challenges will have a limit on total number of submissions you can make. K-fold cross validation (CV) helps solving that problem, by simply splitting the training data into k parts for k iteration of validation. K-fold cross validation also helps solving over-fitting problem, which seemed to be quite an important part of this competition.
<br><br>
![image](http://i.imgur.com/yBJMgqh.jpg)
*An illustration of 3-fold cross validation*
<br><br>
When your CV is strong enough, trust it. Only submit your model when it's relatively high. A low local score but high public leader board score could be a sign of over-fitting, and should be looked into very carefully.
<br><br>
Lastly, scikit-learn does provide a [cross_validation module](http://scikit-learn.org/stable/modules/cross_validation.html) that can be used quite straightforwardly:

{% highlight python %}
from sklearn import cross_validation

cv = cross_validation.KFold(len(train), n_folds=5, shuffle=True,
  indices=False, random_state=1337)
results = []

for traincv, testcv in cv:
  xgbtrain = xgb.DMatrix(train[traincv][list(features)], label=train[traincv][goal])
  classifier = xgb.train(params, xgbtrain, num_rounds)
  score = entropyloss(train[testcv][goal].values, np.compress([False, True],\
      classifier.predict(xgb.DMatrix(train[testcv][features])), axis=1).flatten())
  print score
  results.append(score)

print "Results: " + str(results)
print "Mean: " + str(np.array(results).mean())
{% endhighlight %}

# Blending and stacking
Ensembling might not be used much in practical projects due to its complexity, but it is very important to win Kaggle-like competition. Recently and historically, all competition winners have blended hundreds of their models and solutions together to get a final best result.
<br><br>
Blending also helps in prevent over-fitting. For more information, you can learn more about this topic at MLWave's [Kaggle Ensembling Guide](http://mlwave.com/kaggle-ensembling-guide/).
<br><br>
Do keep in mind that, in order for blending to work efficiently, or to get the highest score, we need to first have single models that work extremely well. For this projects, I simply choose to blend a list of differently-setup XGBoost models (either different hyper parameters, or different feature sets) with [kaggle_avg.py](https://github.com/MLWave/Kaggle-Ensemble-Guide/blob/master/kaggle_avg.py).

# Luck
Admittedly, luck always involved in data science challenges. There are ways to minimize it as discussed above. For this competition, I only managed to finish [#5 in the public leader board](https://challenges.dextra.sg/challenge/44), but ended up in [#1 in private leader board](http://www.dextra.sg/mindef-challenge-results/) with some very close runner-up scores (only ~0.2% better than the second position).
![image](http://i.imgur.com/bmAQS7s.png)

# Lastly:
Below are some great advices from the pro himself, [Owen Zhang](https://www.kaggle.com/owenzhang1), talking about this very topic, in more depth in over-fitting problem, Gradient Boosting Machine's parameter tuning, feature engineering and more:

<iframe width="560" height="315" src="https://www.youtube.com/embed/bek3kbJgNVo" frameborder="0" allowfullscreen></iframe>
