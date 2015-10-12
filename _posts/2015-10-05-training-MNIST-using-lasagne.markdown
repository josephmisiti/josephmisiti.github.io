---
layout: post
title:  "Training MNIST Neural Networks Using Lasagne"
date:   2015-10-05
categories: machinelearning
---

The past few weeks, I have been experimenting with the latest-and-greatest deep learning networks, all written in `python`, to decide which framework I could dive into an become an expert in. After looking at [hebel](), [keras](), [chainer](), and [Lasagne](), I decided to go with Lasagne because of the documentation and tutorials available online. The other frameworks are great, it just seemed like Lasagne currently has the most tutorials and the best docs.

In this blog post, I am going to show you how to use the Lasagne framework to train a neural network on the MNIST database. In later blog posts, I am going to use Lasagne to solve a variety of deep learning problems in natural language processing and computer vision.

All of my work was done on an GPU `g2.8xlarge` rented on Amazon AWS. Specifically, I was able to utilize the following AMI to do this work: `ami-55deaf30`.

To start with, I need to load the MNIST database into a format that `Lasagne` accepts, which happens to be `numpy` matrices. Luckily, I did not need tow write much code here because [mnielsen](https://github.com/mnielsen/neural-networks-and-deep-learning/blob/master/src/mnist_loader.py) did most of the work for me. I did end up writing a single method that utilized his code:

{% highlight python %}
def load(testing=False):
    tr_d, va_d, te_d = load_data()
    if not testing:   
        training_inputs = [np.reshape(x, (784, 1)) for x in tr_d[0]]
        training_results = [vectorized_result(y) for y in tr_d[1]]
        X,y = np.array(training_inputs),np.array(training_results)
        X = np.reshape(X,(50000, 784))
        y = np.reshape(y,(50000, 10))
        X = X.astype(np.float32)
        y = y.astype(np.float32)
        return (X,y)
    else:
        raise
{% endhighlight %}



{% highlight python %}
    
"""
mnist_loader
~~~~~~~~~~~~

A library to load the MNIST image data.  For details of the data
structures that are returned, see the doc strings for ``load_data``
and ``load_data_wrapper``.  In practice, ``load_data_wrapper`` is the
function usually called by our neural network code.
"""

#### Libraries
# Standard library
import cPickle
import gzip

# Third-party libraries
import numpy as np

def load_data():
    """Return the MNIST data as a tuple containing the training data,
    the validation data, and the test data.

    The ``training_data`` is returned as a tuple with two entries.
    The first entry contains the actual training images.  This is a
    numpy ndarray with 50,000 entries.  Each entry is, in turn, a
    numpy ndarray with 784 values, representing the 28 * 28 = 784
    pixels in a single MNIST image.

    The second entry in the ``training_data`` tuple is a numpy ndarray
    containing 50,000 entries.  Those entries are just the digit
    values (0...9) for the corresponding images contained in the first
    entry of the tuple.

    The ``validation_data`` and ``test_data`` are similar, except
    each contains only 10,000 images.

    This is a nice data format, but for use in neural networks it's
    helpful to modify the format of the ``training_data`` a little.
    That's done in the wrapper function ``load_data_wrapper()``, see
    below.
    """
    f = gzip.open('./data/mnist.pkl.gz', 'rb')
    training_data, validation_data, test_data = cPickle.load(f)
    f.close()
    return (training_data, validation_data, test_data)

def load_data_wrapper():
    """Return a tuple containing ``(training_data, validation_data,
    test_data)``. Based on ``load_data``, but the format is more
    convenient for use in our implementation of neural networks.

    In particular, ``training_data`` is a list containing 50,000
    2-tuples ``(x, y)``.  ``x`` is a 784-dimensional numpy.ndarray
    containing the input image.  ``y`` is a 10-dimensional
    numpy.ndarray representing the unit vector corresponding to the
    correct digit for ``x``.

    ``validation_data`` and ``test_data`` are lists containing 10,000
    2-tuples ``(x, y)``.  In each case, ``x`` is a 784-dimensional
    numpy.ndarry containing the input image, and ``y`` is the
    corresponding classification, i.e., the digit values (integers)
    corresponding to ``x``.

    Obviously, this means we're using slightly different formats for
    the training data and the validation / test data.  These formats
    turn out to be the most convenient for use in our neural network
    code."""
    tr_d, va_d, te_d = load_data()
    training_inputs = [np.reshape(x, (784, 1)) for x in tr_d[0]]
    training_results = [vectorized_result(y) for y in tr_d[1]]
    training_data = zip(training_inputs, training_results)
    validation_inputs = [np.reshape(x, (784, 1)) for x in va_d[0]]
    validation_data = zip(validation_inputs, va_d[1])
    test_inputs = [np.reshape(x, (784, 1)) for x in te_d[0]]
    test_data = zip(test_inputs, te_d[1])
    return (training_data, validation_data, test_data)

def load(testing=False):
    tr_d, va_d, te_d = load_data()
    if not testing:   
        training_inputs = [np.reshape(x, (784, 1)) for x in tr_d[0]]
        training_results = [vectorized_result(y) for y in tr_d[1]]
        X,y = np.array(training_inputs),np.array(training_results)
        X = np.reshape(X,(50000, 784))
        y = np.reshape(y,(50000, 10))
        X = X.astype(np.float32)
        y = y.astype(np.float32)
        return (X,y)
    else:
        raise
    
def vectorized_result(j):
    """Return a 10-dimensional unit vector with a 1.0 in the jth
    position and zeroes elsewhere.  This is used to convert a digit
    (0...9) into a corresponding desired output from the neural
    network."""
    e = np.zeros((10, 1))
    e[j] = 1.0
    return e

{% endhighlight %}



{% highlight python %}

from lasagne import layers;
from lasagne.updates import nesterov_momentum;
from nolearn.lasagne import NeuralNet;
import numpy as np;

net1 = NeuralNet(
    layers=[  # three layers: one hidden layer
        ('input', layers.InputLayer),
        ('hidden', layers.DenseLayer),
        ('output', layers.DenseLayer),
        ],
    # layer parameters:
    input_shape=(None, 784),  # 96x96 input pixels per batch
    hidden_num_units=100,  # number of units in hidden layer
    output_nonlinearity=None,  # output layer uses identity function
    output_num_units=10,  # 30 target values

    # optimization method:
    update=nesterov_momentum,
    update_learning_rate=0.01,
    update_momentum=0.9,

    regression=True,  # flag to indicate we're dealing with regression problem
    max_epochs=400,  # we want to train this many epochs
    verbose=1,
    )
    
  
X,y = load()  
net1.fit(X, y)

{% endhighlight %}


{% highlight python %}

# Neural Network with 79510 learnable parameters

## Layer information

  #  name      size
---  ------  ------
  0  input      784
  1  hidden     100
  2  output      10

  epoch    train loss    valid loss    train/val  dur
-------  ------------  ------------  -----------  -----
      1       0.06673       0.04498      1.48367  0.48s
      2       0.04156       0.03616      1.14935  0.41s
      3       0.03523       0.03184      1.10660  0.41s
      4       0.03165       0.02910      1.08774  0.41s
      5       0.02921       0.02713      1.07678  0.43s
      6       0.02738       0.02560      1.06937  0.44s
      7       0.02593       0.02437      1.06394  0.44s
      8       0.02475       0.02336      1.05926  0.44s
      9       0.02376       0.02251      1.05548  0.42s
     10       0.02293       0.02179      1.05204  0.42s
     
     ......

    391       0.00703       0.00939      0.74809  0.44s
    392       0.00702       0.00939      0.74772  0.41s
    393       0.00702       0.00939      0.74724  0.42s
    394       0.00701       0.00939      0.74683  0.43s
    395       0.00700       0.00938      0.74634  0.42s
    396       0.00700       0.00938      0.74590  0.42s
    397       0.00699       0.00938      0.74554  0.43s
    398       0.00699       0.00938      0.74508  0.45s
    399       0.00698       0.00938      0.74453  0.43s
    400       0.00698       0.00937      0.74412  0.41s
{% endhighlight %}


TODO - add evaluation stuff here ...