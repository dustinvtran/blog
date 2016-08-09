---
layout: post
title: Importance weighted autoencoders
date: '2015-09-05 03:28:33'
---

A new paper on variational autoencoders is out on arXiv, titled "[Importance Weighted Autoencoders](http://arxiv.org/abs/1509.00519)" by Yuri Burda, Roger Grosse, and Ruslan Salakhutdinov.

It's very well written. As a statistician, I tend to have a hard time understanding the deep learning community's lingo for variational inference. Simple concepts like what the authors specify as the model likelihood, the model prior, and the variational distribution, are all confuddled. The background section summarizes the recent literature on "black box methods" very well. To be clear, here I define a _black box_ variational inference algorithm as a VI algorithm which can be used for (at least) any continuous latent variable model.

It introduces both paradigms I tend to categorize black box methods: (1) the continuous sort of deep generative models which variational autoencoders are used for and apply a reparameterization trick, and (2) the most general but higher variance methods, which share the common theme of using a REINFORCE gradient.

The main contribution of the paper comes from a simple derivation of importance sampling in the ELBO. With more than one sample, this can lead to a better lower bound on the marginal likelihood. While the derivation is simple, the distinction from a minibatch of samples to estimate the ELBO is very subtle. Here's the importance weighted ELBO:
\begin{equation}
\mathcal{L}\_k(\mathbf{x}) =
\mathbb{E}\_{\mathbf{h}\_1,\ldots,\mathbf{h}\_k\sim q(\mathbf{h}\mid\mathbf{x})}
\left[
\log \frac{1}{k} \sum_{i=1}^k \frac{p(\mathbf{x},\mathbf{h}\_i)}{q(\mathbf{h}\_i\mid\mathbf{x})}
\right]
\end{equation}

Here's a minibatch ELBO:
\begin{equation}
\mathcal{L}\_{minibatch}(\mathbf{x}) =
\frac{1}{k}\sum_{i=1}^k 
\left[
\log \frac{p(\mathbf{x},\mathbf{h}\_i)}{q(\mathbf{h}\_i\mid\mathbf{x})}
\right],
\qquad
\mathbf{h}\_1,\ldots,\mathbf{h}\_k\sim q(\mathbf{h}\mid\mathbf{x})
\end{equation}

Aside from the formulaic difference in the log-addition rather than log-multiplication, the conceptual difference is very clear once you unpack things. The minibatch ELBO is simply a Monte Carlo estimate of the ELBO. That is it estimates the "single sample" $\mathbf{h}\sim q(\mathbf{h}\mid\mathbf{x})$ expectation. The importance weighted ELBO _augments_ the expectation by considering not one but a fixed number of samples from the variational distribution, which is then used to take the expectation with respect to:
\begin{equation}
\mathcal{L}\_k(\mathbf{x}) =
\int
q(\mathbf{h}\_1\mid\mathbf{x})\cdots\int
q(\mathbf{h}\_k\mid\mathbf{x})
\left[
\log \frac{1}{k} \sum_{i=1}^k \frac{p(\mathbf{x},\mathbf{h}\_i)}{q(\mathbf{h}\_i\mid\mathbf{x})}
\right]
d\mathbf{h}\_1\cdots d\mathbf{h}\_k
\end{equation}
However, it still looks remarkably similar if you use a minibatch of size $k$ to estimate the ELBO and a single set of $k$ samples to estimate the augmented ELBO. In terms of computation, these two are equivalent. It's not clear to me why the importance weighted ELBO is better.

The paper puts itself into context very well in the related works section. Also is very reminiscent of the recent work of Bornschein & Bengio (2015) on the reweighted wake sleep algorithm (which is also well-written).


## References
* Jörg Bornschein and Yoshua Bengio. Reweighted Wake-Sleep. [_arXiv preprint arXiv:1406.2751_](http://arxiv.org/abs/1406.2751), 2015.
* Yuri Burda, Roger Grosse, and Ruslan Salakhutdinov. Importance Weighted Autoencoders. [_arXiv preprint arXiv:1509.00519_](http://arxiv.org/abs/1509.00519), 2015.