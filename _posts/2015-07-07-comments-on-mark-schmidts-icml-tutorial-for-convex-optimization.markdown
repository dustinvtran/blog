---
layout: post
title: Comments on Mark Schmidt's ICML tutorial for convex optimization
date: '2015-07-07 09:10:19'
---

Yesterday was a series of tutorials at ICML, and the ones I attended were all quite insightful—for instance, [Emily Fox's](http://www.stat.washington.edu/~ebfox/) coherent map of Bayesian models of time series analysis (and it was the first time I saw use of a hyper Markov prior in practice!).

[Mark Schmidt](http://www.cs.ubc.ca/~schmidtm/) presented one on convex optimization, which I commented on briefly to a circle of friends. As I've started to repeat many of the same points, I decided to just put them up here in more detail. It was a great talk from the ground up in the canonical point of view of optimization, in which one lays out the assumptions required in order to achieve better convergence rates. All theory in optimization is generally in the regime of Lipschitz continuity, (strong) convexity, and linear differentiable functions. All bets are off when one wanders out of this set of assumptions and only empirical studies can really be made.

There is a lot of interest lately in SAG (Le Roux et al., 2012) and SVRG (Johnson and Zhang, 2013), where their main claim to fame from other stochastic gradient methods is linear convergence rate for strongly convex functions. Stochastic gradient methods in general can only achieve sublinear convergence at best. There was also mention of a few other promising methods, e.g., ASGD from Polyak and Juditsky (1992) which averages the iterates in order to achieve asymptotic optimality, and dual methods which perform optimization on the dual of the objective function. Here are some comparisons of these common stochastic gradient methods:

### SAG
* +: linear convergence rate for strongly convex functions
* -: must store memory of gradient (only heuristics exist to compensate for this), can’t work for streaming data, no work on asymptotics

### SVRG
* +: linear convergence rate for strongly convex functions
* -: hyperparameter for inner loop, can’t work for streaming data, no work on asymptotics

### ASGD
* +: asymptotically efficient, works for streaming data
* -: sublinear convergence rate O(1/t) for strongly convex functions

There's a footnote here on the advertised linear convergence rate for strongly convex functions however. It is a bit misleading as it is a convergence rate to the _maximum likelihood estimate_, __not__ the ground truth. The maximum likelihood estimate asymptotically converges to the truth at the sublinear rate $O(1/t)$, so if the goal is to recover the ground truth then such methods do not help.

This relatively unknown fact more generally addresses the widespread problem in the optimization community in that few consider the statistical properties when viewing these methods as estimators. Ironically, stochastic gradient descent was originally developed under the statistical theory of stochastic approximations by Robbins and Monro (1951), and it has seen little exposure since then. Convergence rates, which are the primary focus of the optimization community, is simply analysis of the convergence rate of the asymptotic bias of these estimators. It is natural to consider other properties, e.g., the asymptotic variance of the stochastic gradient estimators so as to minimize it. 

Moreover, the theoretical results are violated for more complicated models in which the objective function is either nonconvex (e.g., in Bayesian inference using variational methods for mixture models) or nonlinear (e.g., MLE for many statistical models). While it's difficult to make any theoretical progress outside of these nice set of assumptions, it is ultimately where we must progress; it is illogical to use the "best" stochastic gradient method on complex models where the assumptions do not even hold anyways.

## References
* Rie Johnson and Tong Zhang. Accelerating Stochastic Gradient Descent using Predictive Variance Reduction. _NIPS_, 2013.
* Nicolas Le Roux, Mark Schmidt, and Francis Bach. A Stochastic Gradient Method with an Exponential Convergence Rate for Finite Training Sets. _NIPS_, 2012.
* Boris T. Polyak and Anatoli Juditsky. Acceleration of stochastic approximation by averaging. _SIAM Journal on Control and Optimization_, 30(4), 838–855, 1992.