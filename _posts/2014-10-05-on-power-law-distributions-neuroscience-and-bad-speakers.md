---
layout: post
title: On power law distributions, neuroscience, and bad speakers
date: '2014-10-05 01:26:27'
---

Statistical techniques have been increasingly more accepted as an empirical method for solving domain-specific problems. We introduce the power law here, a short description of its discussion in relation to neuroscience, and my thoughts of a recent talk regarding this.

A _power law distribution_ is a distribution whose density function is of the form
$$
p(x) \propto L(x) x^{-\alpha},
$$
where $\alpha>1$ and $L(x)$, known as the _slowly varying function_, satisfies $\lim\_{x\to\infty} L(tx)/L(x)=1$ for some $t>0$. The intuition behind this is to require a probability distribution which is (asymptotically) scale invariant, and $L(x)$ is the direct result of that property [1]. 

Perhaps the most well-known power law distribution is the _Pareto distribution_, which is defined such that for $X\sim\textrm{Pareto}(x\_m,\alpha)$,
$$
P(X > x) = \left(\frac{x\_m}{x}\right)^\alpha, \text{ if }x\geq x\_m
$$
and $P(X> x) = 1$ otherwise, where $x\_m$ is the minimum value in the domain of $X$ and $\alpha>0$.

In general, power law distributions are most often used to represent measurements with heavy tails, skew, and which are functions of some power. A common example is the distribution of wealth among individuals, since a high proportion of a population has low income while a small minority shares the most wealth. This can also be thought in terms of the _Pareto principle_, which is a rule of thumb that can be derived from this property: "roughly 80% of the effects come from 20% of the work".

A recent talk I attended—which I will leave anonymous for the sake of not calling out the speaker but which can still be found quite easily—centered on the hypothesis that the power law is most related to neural activity in vivo—the "active" brain—rather than in vitro. Activity spikes were recorded using humans and various animals such as cats and monkeys, and several key metrics were used: spectral, avalanche, spatiotemporal, and balance dynamics.  The underlying conclusion of the experiment was that activity displayed a distribution with no relationship to the power law, which contradicts previous established hypotheses. The speaker claims that this is because all previous research lacked enough data in order to be statistically significant.

I must be frank in saying that the speaker's talk was incomprehensible. It was almost purposefully cryptic by throwing domain-specific jargon and incredibly dense and unlabelled charts at an audience of computational scientists—almost all of which have very little knowledge of his domain.  Perhaps this was unintentional, but the immediate result is a feeling of alienation: few members of the audience came out understanding the results of his research. This is already barring the fact that a little less than half the audience had up and left by the time he was finishing (this was astonishing to witness).

I cannot imagine that the field of neuroscience is so deeply engrained in niche terminology that one would be unable to present a revised talk which would be any less rigorous. His results are interesting but nevertheless spurious and hard to grasp an understanding of. It's really unfortunate that this is not an uncommon occurrence, and speakers far too often forget that they are presenting material not to themselves but to a group of individuals who are not already familiar with the research. If there's one lesson to be learned regarding the talk itself, it is this: be aware of your audience.


[1] Feller, W.  An Introduction to Probability Theory and its Applications II (2nd ed.). _Wiley_, 1971.
