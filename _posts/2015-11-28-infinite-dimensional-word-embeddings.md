---
layout: post
title: Infinite Dimensional Word Embeddings
date: '2015-11-28 17:30:15'
---

* Eric Nalisnick and Sachin Ravi. Infinite Dimensional Word Embeddings. [arXiv preprint arXiv:1511.05392](http://arxiv.org/abs/1511.05392), 2015.

## Summary

Word embeddings have been huge for the NLP community ever since Tomas Mikolov's 2013 paper (it's gotten over 1000 citations in 2 years!). The basic idea is to learn a $z$-dimensional parameter vector $w\_i\in\mathbb{R}^z$ associated to each word in a vocabulary. This is important as part of a machine learning pipeline which extracts features for language models, text classification, etc. Not unlike advertisements of discrete Bayesian nonparametric models, Nalisnick and Ravi propose the Infinite Skip-Gram model, which allows the dimension $z$ of the word vectors to grow arbitrarily.

Letting word vector $w\_i\in\mathbb{R}^\infty$, context vector $c\_k\in\mathbb{R}^\infty$, and random positive integer $z\in\mathbb{Z}^+$, they consider multinomial logistic regression with (Eq.3)
$$
p(w\_i,c\_k,z) = \frac{1}{Z} e^{-E(w\_i,c\_k,z)},
$$
where $Z$ is the partition function summing over all $w\_i,c\_k,z$. This is the same as the original model for word embeddings, except now there is an auxiliary variable $z$ and the vectors are infinite-dimensional. This seems intractable given some energy function $E(w\_i,c\_k,z)$, as the partition function requires a summation over countably infinite values of $z$. However, similar to Côté and Larochelle (2015), they show that the following penalized energy function enables finite computation (Eq.4):
$$
E(w\_i,c\_k,z) = -\sum\_{j=1}^z w\_{i,j} c\_{k,j} + \Big[z\log a -\sum_{j=1}^z (w\_{i,j}^2 +  c\_{k,j}^2)\Big].
$$
The first term is the original (unpenalized) energy function, which is the inner product of $z$-dimensional vectors. The second term, the penalty, consists of two expressions: the $L\_2$ penalty forces the word and context vectors to be zero after sufficiently large index, and the $z\log a$ term forces a convergent geometric series for the denominator of the conditional distribution $p(z\mid w, c) $ (Eq.5). Tractability of the conditional distribution is required during optimization.

For inference, they treat $z$ as a nuisance parameter and perform an approximate maximum likelihood estimation. Specifically, they minimize an upper bound to the negative log-likelihood $$\mathcal{L}=-\log \int p(c,z\mid w)~\operatorname{d}z$$ over the word vectors $w$. Similar to how word embeddings require negative sampling for computational efficiency, Nalisnick and Ravi make additional Monte Carlo approximations, such as evaluating Monte Carlo estimates of the gradient of (an upper bound to) this objective.

## Discussion

Very cool idea! I'm always a fan of infinite-dimensional models, not just because they're mathematically interesting but because there's no reason that word vectors should be restricted to a fixed dimensionality. In a streaming setting, for example, the word representations should grow increasingly more complex as more information in the data is available.

Although they don't explicitly state this, what they're really doing is applying a variational lower bound for inference, invoking Jensen's inequality and setting the variational distribution as the prior $~q(z)= p(z\mid w\_i)$ (Eq.7). If readers are familiar with the [score function estimator](http://blog.shakirm.com/2015/11/machine-learning-trick-of-the-day-5-log-derivative-trick/) used for variational inference (Paisley et al., 2012), the Monte Carlo gradient in Eq.10 applies the same trick. Following this interpretation, it surprises me then that the algorithm works so well. Using model priors for variational approximations make for quite a loose lower bound of the log-likelihood.

I highly recommmend reading this paper. It's very well-written and easy to read even if you don't know anything about word embeddings. The related work section places it very appropriately in context. They mention Vilnis and McCallum (2015) a lot, and I'd be very interested to see if they can extend their work to inferring infinite-dimensional distributions of words rather than infinite-dimensional point-estimates of words.
