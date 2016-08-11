---
layout: post
title: 'MuProp: Unbiased Backpropagation for Stochastic Neural Networks'
date: '2015-11-28 04:02:06'
---

Having recently finished some papers with Rajesh Ranganath and Dave Blei on variational models [[1]](http://arxiv.org/abs/1511.02386) [[2]](http://arxiv.org/abs/1511.06499), I'm now a bit free to catch up on my reading of recent literature. I've decided to follow [Hugo Larochelle's](https://twitter.com/hugo_larochelle) format, who has written some incredible notes. Hope this is of interest to others.

* Shixiang Gu, Sergey Levine, Ilya Sutskever, and Andriy Mnih. MuProp: Unbiased Backpropagation for Stochastic Neural Networks. [arxiv preprint arXiv:1511.05176](http://arxiv.org/abs/1511.05176), 2015.

## Summary

There's been a lot of recent work merging variational inference and deep learning. Very much a continuation of neural variational inference from Mnih and Gregor (2014), this paper addresses inference for deep discrete latent variable models. The classic example is the sigmoid belief net, where inference is much more difficult than for continous latent variable models. The stochastic gradient of the ELBO—the objective function in variational inference—has impractically high variance during optimization.

Control variates are the most well-known technique for variance reduction (Paisley et al., 2012; Ranganath et al., 2014). Writing the gradient of the ELBO as $$\mathbb{E}_{q(z)}[\nabla_{\lambda} \log q(z; \lambda) f(z)]$$ where $$f(z)=\log p(x,z) - \log q(z; \lambda)$$, Monte Carlo estimates of

$$
\mathbb{E}_{q(z)}[\nabla_{\lambda} \log q(z; \lambda) (f(z) - h(z))] + \mu,
\quad
\mu = \mathbb{E}_{q(z)}[\nabla_{\lambda} \log q(z; \lambda) h(z)]
$$

are still unbiased, and appropriate choices of the control variate $$h(z)$$ can reduce the variance of the estimator.

The authors propose a control variate based on a first-order Taylor expansion of $$f(z)$$ around a fixed value $$\bar z$$. By doing so, they are able to use gradient information from the model, evaluated at that point:

$$
h(z) = f(\bar z) + f'(\bar z)(z - \bar z).
$$

The gradient of the ELBO simplifies to (Eq.3 in the paper)

$$
\mathbb{E}_{q(z)}\Big[\nabla_{\lambda} \log q(z; \lambda) \Big(f(z) - f(\bar z) - f'(\bar z)(z - \bar z)\Big)\Big] + f'(\bar z)\nabla_\lambda \mathbb{E}_{q(z)}[z].
$$

Of course, calculating $$f'(\bar z)$$ is not feasible because of the discrete variables. To address this they apply a "deterministic mean-field network" as an approximation. This enables backpropagation over the mean values of the discrete distributions, rather than over samples from the discrete distribution. In experiments, they demonstrate faster convergence than other estimators, and better results than other (biased) estimators.

## Discussion

It's an interesting idea to consider Taylor expansions for control variates, and it tackles a problem less spotlighted in the deep learning/variational inference literature. Most work concentrates on inference for continuous latent variable models which is easier.

It is a little hard to read however. There's a lot of review, and with less than a page of details (Section 3) being the novel contribution. This portion is dense, especially Section 3.1, and the authors could have provided more details regarding how the mean-field network works. For example, Figure 1 takes a lot of time digesting as there's not much elaboration. The term "rollout" is introduced once in the figure description and forgotten about for the rest of the paper.

It would have been interesting to see experimental results beyond sigmoid belief networks and their multinomial counterpart. For example, Poisson deep exponential families (Ranganath et al., 2015) are state-of-the-art for  topic modelling, and I'm curious in general how well it works for Poisson latent variables, which have infinite rather than finite support.

In terms of variance reduction techniques, I'm surprised they don't mention conditioning only on the Markov blanket, addressed in Ranganath et al. (2014) and also termed "local learning signals" in Mnih and Gregor (2014). This is the most important variance reduction technique, since the stochastic gradients no longer scale with the number of latent variables but rather with the size of their Markov blanket. Another worthwhile mention is local expectation gradients by Titsias (2015), which analytically calculates the expectations over discrete latent variables with small support. Very relevant for sigmoid belief nets.
