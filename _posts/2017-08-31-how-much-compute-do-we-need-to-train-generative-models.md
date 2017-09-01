---
layout: post
title: How much compute do we need to train generative models?
---

Discriminative models can take weeks to train. It was only until a
breakthrough two months ago by Facebook {% cite goyal2017accurate
--file 2017-08-31 %} that we could successfully train a neural net
exceeding human accuracy (ResNet-50) on ImageNet in one hour. And this
was with 256 GPUs and a monstrous batch size of 8192.
<!-- Unfortunately, most of us mortals have maybe 8 GPUs at most—or for the -->
<!-- very fortunate, at most 8 GPUs per experiment—and do not have help     -->
<!-- from the first authors of ResNets and Caffe. This means in 2017, each    -->
<!-- ImageNet classifier can still take days to a week.                       -->

Contrast this with generative models.  We've made progress in
stability and sample diversity with generative adversarial networks,
where, say, Wasserstein GANs with gradient penalty
{% cite gulrajani2017improved --file 2017-08-31 %} and
Cramer GANs
{% cite bellemare2017cramer --file 2017-08-31 %}
can get good results for generating LSUN bedrooms.
But in communication with
Ishaan Gulrajani, this took 3 days to train with 4 GPUs and 900,000
total iterations; moreover, LSUN
has a resolution of 64x64 and is
significantly less diverse than the 256x256 sized ImageNet.
<!-- : this is especially the case as we do 5 discriminator                -->
<!-- updates per generator update, which is already a 5x slowdown compared -->
<!-- to vanilla GANs per-generator iteration.                              -->
Let's also not kid ourselves
that we perfected density estimation to learn the true distribution of
LSUN bedrooms yet.

Generative models for text are no different. The best results so far for the 1 billion
language modeling benchmark are an LSTM with 151 million parameters
(excluding embedding and softmax layers)
which took 3 weeks to train with 32 GPUs
{% cite jozefowicz2016exploring --file 2017-08-31 %}
and a mixture of experts LSTM with 4.3 billion parameters
{% cite shazeer2017outrageously --file 2017-08-31 %}.
<!-- downsampled ImageNet. -->

This begs the question: how much compute _should_ we expect in order
to learn a generative model?

Suppose we restrict ourselves to 256x256 ImageNet as a proxy for
natural images. If we compare the entropy of the conditional
$$p(\text{class label}\mid \text{natural image})$$
to the unconditional
$$p(\text{natural image})$$, then a simple property in information
theory says that the former is upper bounded by at most $$\log K$$
bits for $$K$$ classes.
Comparing this to the number of bits for $$256\times 256=65,536$$
pixels, each of which take 3 values from $$[0, 255]$$,
then a very modest guess would be 5000 times more bits. We also need to
account for the difference in training methods.  Let's say that the
method for generative models is only 6x slower than that of
discriminative models (5 discriminative updates per generator update;
we'll forget the fact that GAN and MMD objectives are actually more expensive
than maximum likelihood due to multiple forward and backward passes).

Finally, let's take Facebook's result as a baseline for learning
$$p(\text{class label}\mid \text{natural image})$$ in 1 hour with 256 GPUs
and a batch size of 8192. __Then the distribution $$p(\text{natural image})$$
would require
1 hour $$\cdot$$ 5000 $$\cdot$$ 6 $$=$$ 30,000 hours $$\approx$$ 3.4 years to train.__
And this is assuming we have the right objective, architecture, and
hyperparameters to set it and forget it: until then, let's hope for
better hardware.

_This short post is extracted from a fun conversation with Alec Radford today._

References
----------

{% bibliography --cited --file 2017-08-31 %}
