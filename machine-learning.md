Machine Learning
================

More specifically, deep learning using Tensorflow.

Cribbed from [machine learning crash course](https://developers.google.com/machine-learning/crash-course/).

Supervised machine learning
---------------------------

ML systems learn how to combine input to produce useful predictions on never-before-seen data.

_Supervised_ ML requires existing, correctly __labeled examples__ to train with. This is in contrast with unsupervised ML, which doesn't have any initial training data. This doc focuses on supervised ML.

* Label: the thing you want to predict (the output, or `y`)
* Features: the input variables describing our data (the input, or `x`)

* Example: an instance of input data `x`
* Labeled example: input paired with a _known_ output `(x, y`)
* Unlabeled example: input with _unknown_ output `(x, ?)`

A simple example of this is __linear regression__. Given a set of points (labeled examples), find a line that fits the data. 

This line allows you to predict output for a given input.

The kinds of problems ML can solve
----------------------------------

Supervised ML can perform:
  * Classification: which of `n` labels? (e.g. is this a cat, dog, or mouse?)
  * Regression: predict numerical values
  * Generation: complex output (e.g. image captions, translations)

Unsupervised ML can perform:
  * Clustering: most similar to other examples

Loss
----

* Loss: The difference between the predicted `y'` and the actual `y`
  * A common way to calculate loss for regression: __L2 Loss__
    * Square of the difference between prediction and label.
    * `(y - y')^2`
    * Graphed, loss function is a parabola. Minimum loss is at the bottom!
      * i.e. our goal is the _minimum_ of the loss curve.

When training our ML system, our goal is to reduce loss.
  * Feed features into model, it spits out predicted outputs.
  * Compare our output with labeled examples and calculate loss.
  * Based on that loss, adjust feature weights and iterate again.
    * Features are assigned a "weight" that determine its predictive power.
    * The derivative points us which we we need to move on our loss curve (gradient).
    * With each step, we move closer to the minimum (bottom of curve).
    * This process is called __gradient descent__.

__Step size__ is the size of the steps you take along your loss curve to zero in on the minimum.
  * If your rate is too small, it takes too long to find the minimum.
  * If your rate is too big, you overshoot on either side of the minimum.
  * An ideal rate gets you to the minimum in the smallest number of steps.

__Learning rate__ is the parameter than controls step size.
  * Scalar multiplier on the gradient vector.

How do you perform these loss checks on big sets of examples?
  * Stochastic Gradient Descent (SGD): one example at a time.
  * Mini-Batch Gradient Descent: batchs of examples, average the loss and gradient.

Tensorflow
----------

At bottom, a library for performing calculations over tensors. Tensors are collections of points (usually floating point numbers) with a size and shape:
  * Scalar: single point ("0-dimension")
  * Vector: 1-dimension set of points
  * Matrix: 2-dimension set of points
  * Tensor: generally, an n-dimension set of points.

Layered on top of this tensor API are some higher-level APIs for supervised machine learning.
  * e.g. [Estimators API](https://www.tensorflow.org/programmers_guide/estimators)

Exercises to try:
  * [First steps with Tensorflow](https://colab.research.google.com/notebooks/mlcc/first_steps_with_tensor_flow.ipynb)

Generalization
--------------

__Generalization__ is the ability of your model to make accurate predictions with never before seen data – that is, data you have not trained on.

If your training dataset is not representative of other datasets, your model may not generalizae well.
  * This is called "overfitting".

Generally speaking, the _less complicated_ the model, the _more generalizable_ the model.

When developing a model, it's a good idea to have both
  * A __training__ dataset to build the model
  * A __test__ dataset to determine how generalizable it is.

When gathering training and test data from a distribution:

  1. Draw examples independently and identically (iid) at random
  2. The distribution is stationary (doesn't change over time)
  3. Pull all examples from the same distribution!

__NB:__ Debugging in ML is often _data debugging_ rather than code debugging.

Creating features ("representation")
------------------------------------

Recall that  __features__ are the input variables describing our data (the input, or `x`).

How do we turn our data into features? We need to turn them into tensors.
  * Numbers: no real conversion is necessary. Just put them in a tensor.
  * Strings: this is obviously trickier!
    * Enumerate: enumerate all possible values of the string.
    * One-hot encoding: if we only care about one string value, convert that to `1`, and all other values to `0`.

Enumeration as a means to represent string data is only really useful if you have a reasonably sized set of enums (for example, 50 state postal codes).

If each enum is nearly unique, then you're not modeling much at all! It's not generalizable.

Bucketing or "binning" is another useful tool. Create buckets for your data (for example, for a set of geolocation data, bucket it by state).

Feature crossing
----------------

Sometimes we may have features (inputs) which produce better models when they're weighted together than alone.

For example, imagine we're building a model with latitude and longitude as features.

Alone, latitude and longitude features may not tell you much. However, if we __cross__ these features, we have a more useful feature:

```
[longitude x latitude]
```

This is a more precise location in the real world.

Regularization for simplicity
-----------------------------

Recall in our discussion of generalization that as a rule, less complex models are more generalizable. Complex models are prone to __overfitting__ their training data.

__Regularization__ can help prevent this by penalizing overly complex models.

If model complexity is a function of weights, a feature weight with a high absolute value is more complex than a feature weight with a low absolute value. 

We can quantify complexity using the __L2 regularization formula__, which defines the __regularization term__ as the sum of the squares of all the feature weights.

Once you have the __regularization term__ (which is a rough measure of model complexity), you can tune its impact by multiplying it by a scalar known as a __lambda__ or __regularization rate__. The larger the lambda, the more regularized the model.

Regularizing for simplicity in this way has the following effects:
  * Encourages weight values toward 0 (but not exactly 0)
  * Encourages the mean of weights toward 0, with a normal (bell-shaped) distribution.

In plain English, you're forcing the model to be less complex, and hopefully more generalizable.

Logistic regression
-------------------

So far our examples have relied on linear regression for our models. It's suitable for predicting numbers with a wide range.

What if the range we care about is very small? Say, we only care about two values, like 0 or 1?

__Logistic regression__ is a better model for predicting __probability__ or __binary classification__, because it produces some value between 0 and 1!
  * Click or not click?
  * Spam or not spam?
  * Friend or foe...?

Resources:
  * [Logistic regression exercise](https://colab.research.google.com/notebooks/mlcc/logistic_regression.ipynb).

Classification
--------------

A __classification__ is a threshold applied to a probability. It's a way to use logistic regression to well, _classify_ things!

For example, say we want to classify spam. We use logistic regression, and decide that any `y' > 0.6` is spam. That's a classification!

You'd think that accuracy would be a good metric for classification models. But what if a class is very rare, only 0.1% of results? It would be very easy to create a model that's "99.9% accurate", but actually fails miserably at classification.

This kind of problem is called a __class imbalance__. Class imbalanced problems can cause false positives or false negatives.

We have better metrics than mere "accuracy" to detect false positives and false negatives:

* __Precision__: `(true positives) / (true positives + false positives)`
  * How many times did we "cry wolf"?

* __Recall__: `(true positives) / (true positives + false negatives)`
  * Did we miss any wolves?

Depending on the problem we're trying to solve, we may optimize for precision, or optimize for recall. The improve precision, we may _increase_ or classification threshold. To improve recall, we may _decrease_ our classification threshold.

Regularization for sparsity
---------------------------

If we have a lot of features (and therefore weights), we can start to use a lot of RAM. Can we reduce the number of weights?

One thing we can do is try to remove weights that are equal to 0, since they're not doing anything anyway.

__L1 regularization__ is a form of regularization that pushes weights closer to 0 so they can be filtered out.

Compare this with L2 regularization (see "regularization for simplicity"), which uses the square of weights as the regularization term. Since we're using the square, the degree of regularization increases exponentially with the size of the weight.

By contrast, L1 regularization has a linear regularization term, so all weights are regularized at an equal rate.

You can think of L1 regularization as a high-pass filter for our weights.

Neural Networks
---------------

Some problems are _non-linear_ and do not easily confirm to either linear or logistic regression. One option is to use feature crosses to make non-linear problems "look" linear (see "feature crosses" above). But do we have other options?

We can __layer__ linear models, by feeding the outputs of one model to the inputs of another. But we're still dealing with a linear model overall.

However, we can apply some transformations to our outputs before we pass them on to the next layer. Moreover, these transformations can be _non-linear_. These transformations are called __activation functions__.

Some common activation functions:

* Rectified Linear Unit (ReLu): if less than zero, turn it into zero, otherwise leave it alone.
* Sigmoid: since it ranges from 0 to 1, useful for probabilities/classification.

By layering models in this way and passing the outputs through activation functions, we have built a __neural network__! Sometimes they're also called "deep models".

The neural network trains itself by __backpropagating__ the calculated loss and adjusting the weights accordingly.

Resources:
 * [Tensorflow exercise with neural nets](https://colab.research.google.com/notebooks/mlcc/intro_to_neural_nets.ipynb).
 * [Backpropagation algorithm demo](https://google-developers.appspot.com/machine-learning/crash-course/backprop-scroll/)

Normalization
-------------

Neural networks benefit greatly from data normalization. Some common techniques include:

* Linear scaling: brings feature values within the range of -1 to 1.
* Clipping: cut off min and max values beyond a chosen scale.
* Remove outliers: more often than note these are not at all helpful for our model.

Multi-class neural nets
-----------------------

Logistic regression are useful for binary classification. But what about multi-class problems? Say, for example, if we wanted to identify pictures of animals (is this a cat, dog, or monkey?)

Multi-class neural nets have a unique output for each possible class (`output => class`). 

* One-vs-all multi-class 
  * We train a particular output on a signal of "my class" vs "all other classes".
  * You can do this with a single deep network, or with several models.
  * You could conceivably get a match on multiple classes.
  * What if we want to force the model to just pick one class? See the following:
* SoftMax multi-class
  * Adds constraint to one-vs-all: require output of all one-vs-all nodes to sum to 1.0
  * We can interpret that output as _probabilities_ of whether a given input belongs to a given class.
  * Useful if we want to pick "one correct" output.

Resources:
  * [Multi-class classification](https://colab.research.google.com/notebooks/mlcc/multi-class_classification_of_handwritten_digits.ipynb)

Embeddings
----------

`d`-Dimensional __embeddings__ allow you to categorize things by some dimensions (or __aspects__). 

For examples a 2-dimensional classification of movies might be `arthouse <=> blockbuster` and `kids <=> adults`. We can plot movies along these two dimensions. Each movie becomes a `d`-dimensional point where the value in dimension `d` represents how much the movie fits that aspect.

Most importantly, these embeddings can be learned from data. We can take a list of all movies watched by a user:

```
user1 => [0, 1, 0, 1]
user2 => [0, 0, 1, 1]
```

These vectors than serve as our features. If we feed these into a deep network with an __embedding layer__, the model can discover some aspects. For example, it might discover a "children's movie" aspect because a particular group of kids movies tend to be watched together.

It's important to note that the _machine_ discovers these embeddings. Some embeddings are intuitive (like movie categorization), others are not intuitive and only make sense to the machine. You just choose the number of dimensions (aspects) in the embedding layer, and your embedding spontaneously emerge. _Similar items_ will be embedded _close to each other_ in `d`-dimensional space.

Static vs Dynamic training
--------------------------

__Statically trained__ models are trained offline, before runtime.
  * Pros: more control over the input data, easy to build and test
  * Cons: retraining must be manually triggered, can get stale

__Dynamically trained__ models are trained online, at runtime.
  * Pros: models are much more up to date
  * Cons: less control over the input data, harder to build and test

Static vs Dynamic inference
---------------------------

__Static inference__ means make all possible predictions in advance, save them, and look it up later.
  * Pros: much faster runtime, fewer failure modes
  * Cons: falls on its face if it gets a input it hasn't already predicted, slow update latency

__Dynamic inference__ means make predictions on the fly at request time.
  * Pros: much more flexible, doesn't require anticipating every possible input, fast update latency
  * Cons: slower runtime, may limit model complexity, more failure modes

ML Best Practices
-----------------

* Keep the first model simple
* Focus on ensuring data pipeline correctness
* Use a simple, observable metric for training and evaluation
* Own and monitor your input features
* Treat your model configuration as code: review it, check it in
* Write down the results of all experiments, especially "failures"

Take a look at [this doc](https://developers.google.com/machine-learning/rules-of-ml/) for more useful guidelines.
