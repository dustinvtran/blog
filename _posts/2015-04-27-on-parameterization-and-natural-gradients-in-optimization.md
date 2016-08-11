---
layout: post
title: On parameterization and natural gradients in optimization
---

I went to a talk by [Roger Grosse](http://www.cs.toronto.edu/~rgrosse/) today, who was presenting his very recent work on a new SGD procedure called [K-FAC](http://arxiv.org/abs/1503.05671) [2]. Using the correct parameterization during optimization is a crucial issue, and it's great that there's more work revolving around this as previous literature has focused mainly on the issue of the learning rate.

The idea of K-FAC boils down to the fact that adaptive methods such as AdaGrad [1], and those that employ momentum with Nesterov's accelerated gradient [3], ultimately do better than the original stochastic gradient descent because they take advantage of second-order (curvature) information. More precisely, they take advantage of the information geometry of the parameter space, as the [natural gradient](https://hips.seas.harvard.edu/blog/2013/01/25/the-natural-gradient/) can be directly written as the matrix-vector multiplication of the inverse Fisher information with the original gradient. (I also like interpreting the natural gradient update simply through [Fisher scoring](http://en.wikipedia.org/wiki/Fisher_scoring).) Thus the question is: at what point can one best approximate the Fisher information while limiting the computational complexity at each iteration?

Their approach is to consider approximating the Fisher information as a block diagonal matrix, which allows one to recover some dependency structure in the curvature between parameters, but not so much that it makes the inversion too slow. Block diagonals allow one to do smaller mini-inversions in parallel and don't come at "too much of a cost" compared to the original iteration complexity for SGD. There's no good theory on the method, and it doesn't easily generalize to arbitrary models, as one has to carefully consider where to make the blocks and where to allow for independence in parameters. Still, I think it's certainly in the right direction past the current adaptive/momentum-type methods.

This also seems to be the right track for variational inference as we can only go so far with mean-field approximations. However, structured approaches that condition on the model can't generalize, and it would be interesting to examine copula-based approaches which don't require such precise dependency knowledge a priori and would collapse some of them in the limit. I'm also curious to when stability might be more closely examined by the optimization/neural net community.

## References
[1] John Duchi, Elad Hazan, and Yoram Singer. Adaptive subgradient methods for online learning and stochastic optimization. *Journal of Machine Learning Research (JMLR)*, 12:2121–2159, 2011.

[2] James Martens and Roger Grosse. Optimizing neural networks with kronecker-factored approximate curvature. [arXiv:1503.05671](http://arxiv.org/abs/1503.05671), 2015.

[3] Yurii Nesterov. A method for solving a convex programming problem with convergence rate $$O(1/k^2)$$. Soviet Math. Dokl., 27, 372–376, 1983.
