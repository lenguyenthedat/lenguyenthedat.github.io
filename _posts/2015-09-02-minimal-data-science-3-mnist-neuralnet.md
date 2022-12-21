---
layout: post
title: 'Minimal Data Science #3: Handwritten Digit Recognition with Convolutional Neural Network'
date: '2015-09-02T20:50:00.001-07:00'
author: Dat Le
tags: code, data science, machine learning, tutorial, aws, python, pandas, scikit learn, kaggle
modified_time: '2015-09-02T20:50:00.001-07:00'
comments: true
featured_image: http://i.imgur.com/MFFhjFZ.jpg
---

![image](http://i.imgur.com/MFFhjFZ.jpg)

## Introduction
This is the third post of my **Minimal Data Science** blog series, the previous posts can be located here:

- [lenguyenthedat.github.io/minimal-data-science-1-starcraft](http://lenguyenthedat.github.io/minimal-data-science-1-starcraft/)
- [lenguyenthedat.github.io/minimal-data-science-2-avazu](http://lenguyenthedat.github.io/minimal-data-science-2-avazu)

<br>
In this post, we will be looking into one of the most well-known Data Science challenges: the [MNIST - Digit Recognizer](https://www.kaggle.com/c/digit-recognizer) and a solution with Convolution Neural Network model.

**Note:** The source codes as well as original datasets for this series will also be updated at this [Github repository](https://github.com/lenguyenthedat/minimal-datascience) of mine.
{: .notice}

## The Challenge
![image](http://i.imgur.com/nmyGH4g.png)

<br>
As described in [Kaggle's Digit Recognizer](https://www.kaggle.com/c/digit-recognizer):

- The goal in this competition is to take an image of a handwritten single digit, and determine what that digit is.
- Evaluation method: [Accuracy Score](http://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html).

## Neural Network
![image](http://i.imgur.com/Jfquh1M.png)

<br>
First and foremost, don't be scared by the term [Neural Network](https://en.wikipedia.org/wiki/Artificial_neural_network) - it is actually easier to use than you might think. Nowadays, you can build your own neural network model by any of these open-source libraries with Python:

- [Theano](http://deeplearning.net/software/theano/)
- [PyBrain](http://pybrain.org/)
- [Nolearn](https://pythonhosted.org/nolearn/)
- [Pylearn2](http://deeplearning.net/software/pylearn2/)
- [Keras](http://keras.io/)
- [Lasagne](http://lasagne.readthedocs.org/en/latest/)
- [Scikit-Learn](http://scikit-learn.org/)
- [Scikit Neural Network](http://scikit-neuralnetwork.readthedocs.org)

<br>For the purpose of this blog, I'm going to use [Scikit Neural Network](https://github.com/aigamedev/scikit-neuralnetwork), simply because it's very similar to using Scikit-learn library.

<br>To install Scikit Neural Network, you need `python` and `pip` ready:

{% highlight bash %}
pip install scikit-neuralnetwork
{% endhighlight %}

## Building Neural Network Model
Scikit Neural Network Classifier pretty much can be used the same way as any other [Scikit-Learn's classifier](http://scikit-learn.org/stable/auto_examples/plot_classifier_comparison.html).
<br>
<br>
**Define your model**
<br>
Below is how you create a Neural Network model with 3 `Convolutional Layers`, 1 `Rectifier Layer`, and 1 `Softmax Loss Layer`:

{% highlight python %}
myNNClassifier = Classifier(
                    layers=[
                        Convolution("Rectifier", channels=16, kernel_shape=(5,5)),
                        Convolution("Rectifier", channels=16, kernel_shape=(5,5)),
                        Convolution("Rectifier", channels=16, kernel_shape=(5,5)),
                        Layer('Rectifier', units=100),
                        Layer('Softmax')],
                    learning_rate=0.01,
                    learning_rule='momentum',
                    learning_momentum=0.9,
                    batch_size=100,
                    valid_size=0.01,
                    n_stable=20,
                    n_iter=200,
                    verbose=True)
{% endhighlight %}

**Training and Evaluation**
{% highlight python %}
# Train
myNNClassifier.fit(np.array(train[list(features)]), train[goal])

# Evaluation
predictions = myNNClassifier.predict(np.array(test[features]))
print 'Accuracy Score:'
print accuracy_score(test[goal].values,predictions)

# Cross Tab
print 'Contingency table:'
print pd.crosstab(test[goal],
    np.array([val for sublist in predictions for val in sublist]),
    rownames=["Pred"], colnames=["Actual"])
{% endhighlight %}

**Output**:

**Note:** 
Even though we only used 1000 data points (comparing to 42000 datapoints from the real training dataset), we already achieved 88.94% accuracy score.
A [full implementation of this solution](https://github.com/lenguyenthedat/kaggle-for-fun/tree/master/digit-recognizer) can achieve 98.71% accuracy score (top 10%).
{: .notice}

{% highlight bash %}
$ python digit-recognizer.py
Initializing neural network with 5 layers, 784 inputs and 10 outputs.
  - Convl: Rectifier  Output: (24, 24)   Channels: 16
  - Convl: Rectifier  Output: (20, 20)   Channels: 16
  - Convl: Rectifier  Output: (16, 16)   Channels: 16
  - Dense: Rectifier  Units:  10
  - Dense: Softmax    Units:  10

Training on dataset of 764 samples with 606,616 total size.
  - Reshaping input array from (764, 784) to (756, 28, 28, 1).
  - Train: 756        Valid: 8
  - Terminating loop after 200 total iterations.
  - Early termination after 20 stable iterations.

Epoch    Validation Error      Time
-----------------------------------
    1        2.305020         17.8s
    2        2.312723         12.1s
    3        2.324267         12.0s
    4        2.332217         11.9s
    5        2.346926         16.2s
    6        2.390786         13.2s
    7        2.419367         14.5s
    8        2.421755         13.0s
    9        2.442673         12.8s
   10        2.447968         22.0s
   11        2.444436         13.0s
   12        2.440250         13.6s
   13        2.448444         13.1s
   14        2.446939         12.3s
   15        2.445710         11.8s
   16        2.447854         11.8s
   17        2.446387         12.8s
   18        2.450307         13.0s
   19        2.450688         12.7s
   20        2.449938         11.9s

Early termination condition fired at 20 iterations.
Accuracy Score:
0.889361702128

Contingency table:
Actual   0   1   2   3   4   5   6   7   8   9
Pred
0       19   0   0   0   0   0   0   0   0   0
1        0  22   0   0   0   0   0   0   0   0
2        0   0  29   0   1   0   0   2   0   0
3        1   0   0  18   0   2   0   1   0   0
4        0   0   0   0  18   0   0   0   0   0
5        1   2   0   0   0  21   0   0   0   0
6        0   0   0   0   0   1  17   0   0   0
7        0   0   0   0   1   0   1  29   0   2
8        0   3   0   1   0   1   1   0  15   0
9        0   0   0   0   1   1   0   2   1  21
{% endhighlight %}
