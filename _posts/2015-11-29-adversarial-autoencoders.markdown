---
layout: post
title: Adversarial autoencoders
date: '2015-11-29 16:19:10'
---

* Alireza Makhzani, Jonathon Shlens, Navdeep Jaitly, and Ian Goodfellow. Adversarial Autoencoders. [arXiv preprint arXiv:1511.05644](http://arxiv.org/abs/1511.05644), 2015.

## Summary

Following the trend in auto-encoders for generative modelling, Makhzani et al. propose an adversarial version using the framework of generative adversarial networks (Goodfellow et al., 2014). I have a bit of trouble following the terminology in these lines of works, so in my summary I will use different notation from the papers, following statistical conventions which I personally find more elucidating. And I'll motivate it differently, coming from various divergence measure arguments.

I'll first review generative adversarial networks (GANs). Suppose we observe data $x$. GANs employ a pair of models $(G,D)$, where $G$ is the generative model and $D$ is a discriminative model to help perform inference on $G$.

* The discriminative model $D$ aims to output high probability for samples $x$ which come from the empirical data distribution $p\_{\text{data}}(x)$, and output low probability for samples $x$ which come from the generative model $p\_{\text{g}}(x)$.
* The generative model $G$ aims to fool the discriminative model, i.e., it consists of a prior $p(z)$ and likelihood $p(x\mid z)$, and it aims to generate data most closely matching the observed data.

The objective follows a minimax game
$$
\min\_G
\max\_D
\mathbb{E}\_{p\_{\text{data}}(x)}[\log D(x)] + \mathbb{E}\_{p\_{\text{g}}(x)}[\log (1- D(x))],
$$
which alternates between learning the discriminator $D$ and learning the generator $G$. The intuition is that finding the optimal $p\_{\text{g}}(x)$ among a class of generative models $G$ corresponds to minimizing the Jensen-Shannon divergence
$$
JSD(p\_{\text{data}}(x) \| p\_{\text{g}}(x)) = \frac{1}{2}\operatorname{KL}\Big(p\_{\text{data}} \Big\| \frac{p\_{\text{data}} + p\_{\text{g}}}{2}\Big) +
\frac{1}{2}\operatorname{KL}\Big(p\_{\text{g}} \Big\| \frac{p\_{\text{data}} + p\_{\text{g}}}{2}\Big).
$$
Minimizing $JSD$ directly is not possible; this is where the objective above, using an additional discriminative model, comes in handy. Recall also that maximum likelihood estimation minimizes $\operatorname{KL}(p\_{\text{data}} \| p\_{\text{g}})$ (White, 1982). Using JSD leads to a more balanced approximation not underfitting or overfitting modes. It's also worth noting that unlike approximate posterior inference, these are divergence measures directly on data distributions for $x$, not on latent variable distributions for $z$. For example, variational inference minimizes $\operatorname{KL}(q(z) \| p(z\mid x))$.

In adversarial auto-encoders, we go back to minimizing divergence measures on the latent variables. Recall that the variational auto-encoder's objective (Kingma and Welling, 2014) is equivalently
$$
\min\_q \mathbb{E}\_{q(z\mid x)}[-\log p(x\mid z)] + \operatorname{KL}(q(z\mid x)\|p(z)),
$$
where the first term is the reconstruction error—with the decoder $p(x\mid z)$ evaluating codes from the encoder $q(z\mid x)$—and the second term is the regularizer. The adversarial auto-encoder's objective simply changes the regularizer to $JSD(p(z)\|q(z))$, where we define the _aggregated posterior_
$$
q(z) = \int q(z\mid x) p\_{\text{data}}(x)\operatorname{d}x.
$$
$JSD(p(z)\|q(z))$ is just as intractable as minimizing $JSD$ in the data space, so it requires an adversarial network: $p(z)$ replaces the original $p\_{\text{data}}(x)$ and $q(z\mid x)$ replaces the original $p\_{\text{g}}(x)$:
$$
\min\_G
\max\_D
\mathbb{E}\_{p(z)}[\log D(z)] + \mathbb{E}\_{q(z\mid x)}[\log (1- D(z))].
$$
To train adversarial autoencoders, one alternates between minimizing the reconstruction error of the auto-encoder, i.e., $\mathbb{E}\_{q(z\mid x)}[-\log p(x\mid z)]$, and training the adversarial network. The rest of the paper goes into standard experiments for semi-supervised learning on MNIST and SVHN. They also comment on an extension of generative moment matching networks (Li et al., 2015) to auto-encoders, also following these ideas.

## Discussion

I see generative adversarial networks as fascinating inference algorithms because they're a rare case where we can minimize a more general divergence measure between the generative model and the data distribution. It makes sense that one should avoid MLE's KL objective, which can lead to overdispersed distributions, in favor of this approach. And it's great to see attempts at trying to port these same ideas to the variational inference/auto-encoder setting.

The experiments seem to show that adversarial auto-encoders lead to higher visual fidelity than variational auto-encoders. It's not clear to me  why this is the case however. The fact that there's even a KL term as a regularizer in the VAE objective is a simple byproduct of the variational lower bound. In the end, it is still minimizing $\operatorname{KL}(q(z\mid x)\|p(z\mid x))$. However, replacing the KL regularizer with $JSD$ makes it unclear what the underlying divergence measure is any longer. I buy that $JSD$ ensures less "holes" as $q(z)$ approximates the prior $p(z)$, but there's a weird conflict as $q$ simultaneously gets trained when minimizing the reconstruction error. The underlying divergence measure, if it exists, is most likely not as simple as $JSD(p(z\mid x)\|q(z\mid x))$—although if it were then that would be fantastic(?). Does this alternating procedure also necessarily converge?

Obligatory comment in relation to hierarchical variational models (Ranganath et al., 2015): Their "universal approximator posterior", as a possible choice for the encoder $q(z\mid x)$, is a hierarchical variational model. To wield the intractability of the density they naively apply Monte Carlo estimates. We've found this not to work well in practice due to very high variance, forcing a more complicated inference procedure which we outline in our paper.

_edit_ (12/1/15): A previous version of this post wrote $\operatorname{JSD}(p(z)\|q(z\mid x))$ instead of $\operatorname{JSD}(p(z)\|q(z))$. Thanks to Alireza Makhzani for the correction.