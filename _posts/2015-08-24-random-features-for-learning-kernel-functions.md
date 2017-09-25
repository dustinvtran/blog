---
layout: post
title: Random features and kernel functions
---

There's this really cool idea for learning nonlinear functions with simple techniques, launched quite a while ago by Rahimi and Recht (2007), and which I haven't fully grasped. Yet, the idea of random projections is becoming more popular these days; the problem of learning higher order functions simply comes up in so many places.

Suppose there are $$N$$ data points $$\{x_i,y_i\}$$ where $$x_i$$ has $$d$$ covariates. In one case of the problem, the task is to train a classifier $$f$$ via

$$
\min \mathcal{L}(f; X,y)\equiv \min \frac{1}{N}\sum_{i=1}^N (f(x_i)- y_i)^2
$$

where $$f$$ has the functional form

$$
f(x) = \sum_{i=1}^N \alpha_i k(x, x_i)
= \sum_{i=1}^\infty \alpha(w_i)\phi(x; w_i)
$$

where the second equality is true by the Representer Theorem, where $$\phi:X\times\Omega\to\mathbb{R}$$ is known as a _feature function_ such that $$\phi(x)^T\phi(y) = k(x,y)$$.

This says that one can avoid training the kernel machine directly, which requires computing a $$n\times n$$ matrix comprised of kernel entries $$k(x_i,x_j)$$; instead, train in the feature space $$\phi(\cdot)$$. This feature space is infinite-dimensional, so we approximate it with one of finite dimension $$k$$:

$$
f(x) \approx \sum_{i=1}^k \alpha(w_i) z(x; w_i),\quad k(x,y) = \phi(x)^T\phi(y) \approx z(x)^Tz(y)
$$

Thus, given $$w_i$$'s and function $$z$$, the task reduces to performing linear regression over the $$z$$'s. We form this finite-dimensional representation using _random features_ to generate $$w_i$$'s and whose distribution induces a functional form for the $$z$$'s.

__Random Fourier features__.

* Generate a random $$k\times d$$ matrix $$W$$, e.g., for each entry $$W_{i,j}\sim\mathcal{N}(0,1)$$.
* Compute the $$k\times N$$ feature matrix $$Z$$, where entry $$Z_{i,j}$$ is the $$i^{th}$$ feature map on the $$j^{th}$$ data point

$$
Z_{ij}=z_i(x_j;
w_i) \equiv \exp(i\cdot w_i^Tx_j)
$$

This implies

$$
\min \mathcal{L}(f; X,y) \approx \min_{\alpha\in\mathbb{R}^k} \sum_{i=1}^k (z_i^T\alpha -
y_i), \quad k(x, y) \approx z(x)^Tz(y)
$$

* Perform linear regression: $$\mathrm{Regress}(y \sim Z)$$, e.g., $$\alpha = (Z^TZ)^{-1}Zy\in\mathbb{R}^k$$.

The result is an approximation to the classifier with the Gaussian RBF kernel.

As confused as I am why this works? There's an [old 2009 video](http://research.microsoft.com/apps/video/default.aspx?id=103390&l=i) of Rahimi presenting the idea in more generality, and also a short overview of the paper in context of previous literature [available here](http://blog.smola.org/post/10572672684/the-neal-kernel-and-random-kitchen-sinks).

To motivate this from a statistics and/or signal processing point of view, Guhaniyog and Dunson (2013) have this really cool paper which uses it for regression in Bayesian statistics.

A table of training time and storage, and test time and storage for this procedure is found in Le et al. (2014; Table 1), which itself is a recent extension which makes the procedure even _more_ easy to store and approximate the Gaussian RBF kernel within some $$\epsilon$$ accuracy.  For a more direct connection to Gaussian processes, a related paper (and the most recent paper related to these ideas) is Yang et al. (2015).


## References
* Rajarshi Guhaniyogi and David B. Dunson. Bayesian Compressed Regression. _arXiv preprint: arXiv:1303.0642_, 2013.
* Quoc Viet Le, Tamas Sarlos, and Alexander Johannes Smola. Fastfood: Approximate Kernel Expansions in Loglinear Time. In _International Conference on Machine Learning_, 2013.
* Ali Rahimi and Benjamin Recht. Random features for large-scale kernel machines. In _Neural Information Processing Systems_, 2007.
* Ali Rahimi and Benjamin Recht. Weighted Sums of Random Kitchen Sinks: Replacing minimization with randomization in learning. In _Neural Information Processing Systems_, pages 1–8, October 2009.
* Zichao Yang, Alex Smola, Song Le, and Andrew Gordon Wilson. A la Carte—Learning Fast Kernels. In _Artificial Intelligence and Statistics_, 2015.
