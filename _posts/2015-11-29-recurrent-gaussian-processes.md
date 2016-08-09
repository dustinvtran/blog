---
layout: post
title: Recurrent Gaussian Processes
date: '2015-11-29 21:55:43'
---

* César Lincoln C. Mattos, Zhenwen Dai, Andreas Damianou, Jeremy Forth, Guilherme A. Barreto, and Neil D. Lawrence. Recurrent Gaussian Processes. [arXiv preprint arXiv:1511.06644](http://arxiv.org/abs/1511.06644), 2015.

## Summary

This is another entry into the successful consolidation of Bayesian deep learning. In the Gaussian process community of recent literature, it's been sort of a contentious issue to try to beat the practical success of parametric functions using neural networks, with their infinite-dimensional counterpart (Neal, 1996). Gaussian processes have nice Bayesian nonparametric properties over parametric functions, and they encode a distribution over predicted values (as well as latent representations); this provides a distribution modelling uncertainty and which implicitly regularizes through Bayesian inference. As such, there is no need for many of the heuristics proposed to avoid overfitting such as dropout (which is an approximate Bayesian technique anyways). Further, these deep architectures can be applied for learning salient representations of even the smallest data sets.

The paper is very much a spiritual successor to the feedforward architecture proposed in Damianou and Lawrence (2013) for composing deep Gaussian processes. Here, Mattos et al. propose a recurrent architecture for composing recurrent (deep) Gaussian processes. It's a simple modelling change: replace the compositions of functions used in RNN's with compositions of Gaussian processes. Not surprisingly, GPs for time series and state space models are related (Frigola et al., 2014; Damianou and Lawrence, 2015); the only difference is that the composition of Gaussian processes in the recurrent architecture leads to a latent autoregressive structure with far more expressive power. I view this difference similarly as the difference between GPLVMs (Lawrence, 2005) and deep GPs (Damianou and Lawrence, 2013).

Inference is non-trivial, and it requires somewhat involved calculations that apply tricks from variational inference on Bayesian GPLVMs (Titsias and Lawrence, 2010) over and over. They are able to set optimal variational distributions for certain latent variables such as the inducing inputs, and generally require a mean-field approximation. They also apply an inference network using an RNN, to avoid learning the massive number of local variational parameters: each layer-wise function of the RNN is the output of the corresponding layer in the composition of variational parameters.

## Discussion

This is definitely one of my favorite ICLR submissions this year. I see the main contribution of this work as solving two issues: 1. maintaining internal memory representations of the data using a recurrent architecture for GPs, and 2. increasing the scalability of deep GPs. 

Any foray into sequence modelling explains why point 1 is really cool. Point 2 is a problem in the original deep GP by Damianou and Lawrence (2013), as the number of variational parameters grows linearly with the size of the data. This makes deep GPs impractical on massive data sets in which neural networks are particularly advantageous. The authors solve this issue here using an RNN as the inference network; moreover, the particular layer-by-layer application of the RNN to output each set of local variational parameters is a pretty unique setup.

On modelling, I didn't follow the intuition behind what the shared weights in RNNs correspond to in recurrent GPs (shared kernel hyperparameters?).

The model sizes studied in the experiments are rather small, using 1 or 2 hidden layers with at most 100 inducing points. Similar to the deep GP paper, it would be  insightful to study varying layers of the recurrent GP and how this impacts performance. How well the recurrent GP maintains internal memory should also be explored. For instance, one can evaluate a standard benchmark on character-to-character sequences in text.

It would be very interesting to explore GRU and LSTM versions, as well as applications in which the recurrent GP employs attention. Perhaps the latter may also avoid the need for development on convolutional GPs.
