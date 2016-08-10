---
layout: post
title: Denoising Criterion for Variational Auto-Encoding Framework
---

* Daniel Jiwoong Im, Sungjin Ahn, Roland Memisevic, and Yoshua Bengio. Denoising Criterion for Variational Auto-Encoding Framework. [arXiv preprint arXiv:1511.06406](http://arxiv.org/abs/1511.06406), 2015.

## Summary

Auto-encoders are used to learn representations of observed data $x$. They do so by minimizing the reconstruction error of a decoder $p(x\mid z)$, given a characterization of hidden variables $z$ from an encoder $q(z\mid x)$. Along with a penalty $P\_\alpha$ for regularization, this can be written as maximizing the objective
$$
\mathcal{L}\_{\text{ae}} = \mathbb{E}\_{q(z\mid x)}[\log p(x\mid z)] - P\_\alpha
$$
over the encoder and decoder's parameters. However, it's unclear what a principled choice of the penalty is, as well as the choice of the encoder for a given decoder $p(x\mid z)$. Variational auto-encoders (Kingma and Welling, 2014; Rezende et al., 2014) reinterpret the evidence lower bound prominent in variational inference for this purpose:
$$
\mathcal{L}\_{\text{vae}} = \mathbb{E}\_{q(z\mid x)}[\log p(x\mid z)] - \operatorname{KL}(q(z\mid x)\| p(z)),
$$
where $p(z)$ is a model prior placed over the hidden variables of the encoder. The penalty term is automatically given as the KL divergence between the encoder and the prior, and the encoder can more generally be specified following variational inference literature. This is now a probabilistic encoder-decoder, with a principled objective given by a lower bound to the model evidence $\log p(x)$.

In this paper, Im et al. take ideas from denoising auto-encoders (Vincent et al., 2008) and apply it to variational auto-encoders. In particular, they consider a _corrupted distribution_ $q(x'\mid x)$, such that
$$
q(z\mid x) = \int q(z\mid \tilde{x})q(\tilde{x}\mid x)\operatorname{d}\tilde{x}.
$$
With a distribution to model corrupted noise, the augmented $q(z\mid x)$ is more expressive than the original $q(z\mid x)$ without corruption: it is able to model additional uncertainty around the data inputs.

For inference, plugging this variational distribution into the ELBO leads to an analytically intractable objective, as the log-density $\log q(z\mid x)$ cannot be computed. As a proxy, they propose maximizing the objective function
$$
\mathcal{L}\_{\text{dvae}} = \mathbb{E}\_{q(\tilde{x}\mid x)}\Big[\mathbb{E}\_{q(z\mid x)}\Big[\log\frac{p(x, z)}{q(z\mid\tilde{x})}\Big]\Big],
$$
which they show is in fact an upper bound to the original ELBO. They also claim it is still a lower bound to the model evidence $\log p(x)$, and thus still a valid variational objective (I'll comment on this later). Gradients proceed as typically done in the literature, either using the reparameterized gradient for differentiable latent variables, or the score function estimator in the more general case. They have an interesting Q&A format for discussing their experiments that I found kind of cool.

## Discussion

This paper is eerily similar to a paper Rajesh Ranganath, Dave Blei, and I uploaded just two weeks earlier than theirs, on [Hierarchical Variational Models](http://arxiv.org/abs/1511.02386). And that was uploaded only three weeks ago from today! Man, the field moves fast.

* The idea is the same: place a prior on inputs to the variational distribution—treating them as _variational_ latent variables—and proceed to marginalize them out. The new inputs are now the inputs to the prior distribution. In their case, the prior is on the data input for inference networks, which output local variational parameters. In our case, the prior is on variational parameters more generally. A prior on data input is the special case when the variational model uses an inference network: following our notation, we replace  $q(z;\lambda)$ with the more powerful $q(z;\theta)=\int q(z\mid\lambda)q(\lambda;\theta)\operatorname{d}\lambda$, and use an inference network $\phi$ to output prior hyperparameters $\theta=\phi(x)$ (see end of Section 5).
* The source of inspiration is different: they follow the standard procedure in denoising auto-encoders. We follow the Bayesian framework, viewing the variational distribution as a model of the posterior latent variables. These different sources of inspiration motivate difference choices of complexity for the prior: they only consider Bernoulli or Gaussian, whereas from the Bayesian interpretation, it is natural to study more complex priors such as the normalizing flow (Rezende and Mohamed, 2015) as a prior; we show the latter leads to more expressive approximations and also more scalable computation. (And in our more recent paper on the [Variational Gaussian Process](http://arxiv.org/abs/1511.06499), we use an even cooler prior :^).
* The variational objectives are different, and I have concerns that their objective is not in fact a lower bound on $\log p(x)$.¹ This means the values of the objective function are no longer a proxy for the model evidence. Among a few things, this invalidates the experimental results, as they compare to methods based on reported variational bounds. Further, maximizing their objective can lead to poor approximations, as there is no stopping the function to blow up to arbitrary values as the global maximum. Actually, it surprises me that the maximized variational objective values in the experiments are still close to those in $\mathcal{L}_{\text{vae}}$. Would be very interested to hear from the authors on these issues.

Regardless of whether the theorems and experiments are correct, denoising variational auto-encoders is a much needed idea. It provides great intuition for why one should use hierarchical instantiations of variational models. Moreover it's always interesting to see the same ideas inspired from a different field. These intuitions especially go nicely with our [VGP paper](http://arxiv.org/abs/1511.06499), in which we make more explicit the auto-encoder connection for learning variational models with latent variables. Maybe we'll add these details in a future revision.

Minor point: They claim that injecting noise both at the data input level ("denoising auto-encoder") and the stochastic hidden layer level ("variational auto-encoder") can be advantageous. However, they're really one and the same, which I'm sure they're aware of as they wrote Lemma 1 in this light. That is, the corrupted hidden variable $\tilde{x}$ is just another stochastic hidden layer, which is marginalized out. This makes the abstract and introduction a little confusing.

¹ In the proof of Lemma 1, when they try to show that $\log p(x)$ upper bounds $\mathcal{L}\_{\text{dvae}}$, there are three expectations expanded out in the first line. If the two expectations regarding the distribution $q(z'\mid x)$ are more explicitly written, it is clear why the $q$'s do not necessarily cancel:
\begin{align}
\mathbb{E}\_{q\_{\psi}(z''\mid x)}
\mathbb{E}\_{q\_{\psi}(z'\mid x)}
\Big[
\log
\mathbb{E}\_{q\_{\varphi}(z\mid z'')}
\Big[
\frac{p\_\theta(x,z)}{q\_{\varphi}(z\mid z')}
\Big]
\Big]
\ne
\mathbb{E}\_{q\_{\psi}(z''\mid x)}
\mathbb{E}\_{q\_{\psi}(z'\mid x)}
[
\log
p\_\theta(x)].
\end{align}
When discussing this with Rajesh Ranganath, he points out a simple counterexample where $\mathcal{L}\_{\text{dvae}}>\log p(x)$: Consider a one-dimensional latent variable model, with $p(x,z)=\operatorname{Bernoulli}(z;0.5)$. Let
\begin{align}
q(\tilde{x}\mid x) &= \operatorname{Bernoulli}(\tilde{x}; 0.5),~
q(z=1\mid \tilde{x}=1) = 0.1,~
q(z=1\mid \tilde{x}=0) = 0.9.
\end{align}
Then $q(z\mid x)$ is Bernoulli with probability $0.5\cdot0.1+0.5\cdot0.9=0.5$, and
$$
\mathcal{L}\_{\text{dvae}} =
\mathbb{E}\_{q(\tilde{x}\mid x)}\Big[\mathbb{E}\_{q(z\mid x)}\Big[\log\frac{p(x, z)}{q(z\mid\tilde{x})}\Big]\Big]
$$
$$
= \mathbb{E}\_{q(\tilde{x}\mid x)}\Big[0.5\log\frac{0.5}{q(z=1\mid\tilde{x})}+0.5\log\frac{0.5}{q(z=0\mid\tilde{x})}\Big]
$$
$$
= 0.5\Big[0.5\log\frac{0.5}{0.1}+0.5\log\frac{0.5}{0.9}\Big]
+
0.5\Big[0.5\log\frac{0.5}{0.9}+0.5\log\frac{0.5}{0.1}\Big]
$$
\begin{equation}
=0.510826...
\end{equation}
But $\log p(x)=0$, implying $\mathcal{L}\_{\text{dvae}}>\log p(x)$. In fact, taking 0.9 in the variational parameter to be arbitrarily close to 1 makes $\mathcal{L}\_{\text{dvae}}$ explode.
