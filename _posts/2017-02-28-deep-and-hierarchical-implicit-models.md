---
layout: post
title: Deep and Hierarchical Implicit Models
---

I'm excited to announce a paper that Rajesh Ranganath, Dave Blei, and
I released today on arXiv, titled
[Deep and Hierarchical Implicit Models](https://arxiv.org/abs/1702.08896).

Implicit probabilistic models are all about sampling as a primitive:
they define a process to simulate observations and do not require tractable
densities
({% cite diggle1984monte --style apa-text --file 2017-02-28 %},
{% cite hartig2011statistical  --style apa-text --file 2017-02-28 %})
. We leverage this fundamental idea to develop new classes of
models: they encompass simulators in the scientific communities,
generative adversarial networks
{% cite goodfellow2014generative --file 2017-02-28 %},
and deep generative models such as sigmoid
belief nets
{% cite neal1990learning --file 2017-02-28 %}
and deep latent Gaussian models
({% cite rezende2014stochastic --style apa-text --file 2017-02-28 %},
{% cite kingma2014autoencoding  --style apa-text --file 2017-02-28 %}).
These modeling developments could not really be done without
inference, and we develop a variational inference algorithm that
underpins them all.

Biased as I am, I think this is quite a dense paper—chock full of
simple ideas that are rife with deep implications. There are many
nuggets of wisdom that I could ramble on about, and I just might in
separate blog posts. By combining the key strengths of probabilistic graphical
models and generative adversarial networks, we think this is the next
step for Bayesian deep learning.

As a practical example, we show how you can take any standard neural
network and turn it into a deep implicit model: simply inject noise
into certain hidden layers. The hidden units in these layers are now
interpreted as latent variables. Further, the induced latent variables
are astonishingly flexible, going beyond Gaussians (or exponential
families
{% cite ranganath2015deep --file 2017-02-28 %})
to arbitrary probability distributions. Deep generative modeling could
not be any simpler than this!

Here's a 2-layer deep implicit model in [Edward](http://edwardlib.org).

```python
import tensorflow as tf
from edward.models import Normal
from keras.layers import Dense

N = 55000  # number of data points
d = 100  # latent dimensionality

# random noise is Normal(0, 1)
eps2 = Normal(tf.zeros([N, d]), tf.ones([N, d]))
eps1 = Normal(tf.zeros([N, d]), tf.ones([N, d]))
eps0 = Normal(tf.zeros([N, d]), tf.ones([N, d]))

# alternate latent layers z with hidden layers h
z2 = Dense(128, activation='relu')(eps2)
h2 = Dense(128, activation='relu')(z2)
z1 = Dense(128, activation='relu')(tf.concat([eps1, h2], 1))
h1 = Dense(128, activation='relu')(z1)
x  = Dense(10, activation=None)(tf.concat([eps0, h1], 1))
```
The model uses Keras, where `Dense(256)(x)` denotes a fully connected
layer with $$256$$ hidden units applied to input `x`. To define a
stochastic layer, we simply inject noise by concatenating it into the
input of a neural net layer. The model alternates between stochastic
and deterministic layers to generate data points
$$\mathbf{x}_n\in\mathbb{R}^{10}$$.

Check out the paper for how you can work with, or even interpret, such a model.

References
----------

{% bibliography --cited --file 2017-02-28 %}
