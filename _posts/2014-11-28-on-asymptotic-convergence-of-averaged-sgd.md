---
layout: post
title: On asymptotic convergence of averaged SGD
---

Stochastic gradient descent (SGD) has seen wide application for learning problems on large scale data, whether this be for generalized linear models [6], SVMs [1], or neural networks [2] alike.  We review a result by Polyak and Juditsky [3] on the theory of performing SGDs with averaged estimates, which shows that it is asymptotically as good as a second-order method.

Recall that in the typical formulation, the problem is to minimize some loss function:
$$\theta^* = \underset{\theta}{\operatorname{arg min}} Q(\theta) = \sum\_{n=1}^N Q\_n(\theta),$$
where $Q_n(\theta)$ is the only part of the function associated with the $n^{th}$ data point. In the statistical formulation, we can formalize this by assuming all $N$ observations are i.i.d., and the loss function corresponds to the negative log-likelihood for some choice of model and its set of parameters $\theta=(\theta\_1,\ldots,\theta\_p)$.

Let the learning rate $\\{\alpha\_n\\}$ be a specified decreasing sequence. *Stochastic gradient descent* uses the update
$$\theta\_{n+1} = \theta\_n - \alpha\_n\nabla Q\_{i\_n}(\theta\_n),$$
where at each iteration one chooses a randomly sampled data point $i\_n\in
\\{1,\ldots,N\\}$. This is a stochastic approximation procedure [5], and it is proven to converge to a point where the expected loss $\mathbb{E}[\nabla Q(\theta^\*)]=0$; for convex loss, $\theta^\*$ is the global minimum.

**Remark**. It was well-known heuristically that one should sample without replacement and make enough passes of the data for SGD, but not well-understood why this is actally better than sampling with replacement. Recht and Re [4] have an interesting proof for this using noncommutative arithmetic-geometric means.

Here is a table which summarizes the most common optimization methods.

<table border="2" style="width:100%; text-align:center;">
<thead>
  <tr>
    <th>method</th>
    <th>Running time complexity</th>
    <th>Convergence rate</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Newton</td>
    <td>$\mathcal{O}(Np^{2+\epsilon})$</td>
    <td>quadratic</td>
  </tr>
  <tr>
    <td>Quasi-Newton (e.g. BFGS, DFP)</td>
    <td>$\mathcal{O}(Np^{1+\epsilon})$</td>
    <td>superlinear</td>
  </tr>
  <tr>
    <td>Gradient descent</td>
    <td>$\mathcal{O}(Np^{1-\epsilon})$</td>
    <td>linear</td>
  </tr>
  <tr>
    <td>Stochastic gradient descent</td>
    <td>$\mathcal{O}(p)$</td>
    <td>sublinear</td>
  </tr>
</tbody>
</table>
<br>

It's a clear win for SGD in terms of running time. Newton's method requires inverting a Hessian $[\nabla^2 Q(\theta_n)]^{-1}$ and computing the full gradient $\nabla Q(\theta\_n)$ at each step; Quasi-Newton only slightly relaxes this by using a low rank approximation of the Hessian; and gradient descent doesn't use it at all but still requires computing $\nabla Q(\theta\_n)$.

However, this comes at the cost of convergence rates. Naturally, SGD can only obtain a sublinear convergence rate $\mathcal{O}(1/n)$ for convex functions, whereas the above can achieve better convergence by using more information per iteration.

While there will always be a point in which SGD surpasses any of the above methods as the size of the data becomes larger (thus causing running time complexity to be the limiting factor), we can still do better than this.

The simplest variant which improves SGD use *Polyak-Ruppert averaging*:
$$\bar{\theta}\_n = \frac{1}{n}\sum_{i=1}^n \theta\_i$$
That is, take the running average of the estimates during each iteration. We define *averaged stochastic gradient descent* (ASGD) as applying SGD with Polyak-Ruppert averaging.

**Proposition**. (Polyak and Judisty [3]) Asymptotically, ASGD is as fast as a second-order SGD, i.e.,
$$\theta\_{n+1} = \theta\_n - [\nabla^2Q(\theta\_n)]^{-1}\nabla Q\_{i\_n}(\theta\_n)$$
While the convergence rate of a second-order SGD is still sublinear, the constants are much improved. This is reflected in practice [7], and it comes at very little cost due to the simplicity of the small change in implementation.

As I'm currently reviewing the literature on stochastic gradient methods, I'll post the proof on another page as I think it has a nice derivation.

## References

[1] L. Bottou. Online Algorithms and Stochastic Approximations, Online Learning and Neural Networks, Edited by David Saad, *Cambridge University Press*, 1998.

[2] L. Bottou. Stochastic Gradient Learning in Neural Networks, *Proceedings of Neuro-Nîmes 91*, EC2, 1991.

[3] B. Polyak and A. Juditsky. Acceleration of stochastic approximation by averaging. *Automation and Remote Control*, 30(4):838–855, 1992.

[4] B. Recht and C. Re. Beneath the valley of the noncommutative arithmetic-geometric mean inequality: conjectures, case-studies, and consequences. *preprint* [arXiv:1202.4184](http://arxiv.org/abs/1202.4184), 2012.

[5] H. Robbins and S. Monro. A stochastic approximation method. *The Annals of Mathematical Statistics*, pages 400–407, 1951.

[6] P. Toulis, E. Airoldi, and J. Rennie. Statistical analysis of stochastic gradient methods for generalized linear models. *JMLR W&CP*, 32(1):667–675, 2014.

[7] W. Xu. Towards optimal one pass large scale learning with averaged stochastic gradient descent. *preprint* [arXiv:1107.2490](http://arxiv.org/abs/1107.2490), 2011.
