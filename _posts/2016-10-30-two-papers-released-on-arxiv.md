---
layout: post
title: Two papers released on arXiv, "Operator Variational Inference" and "Model Criticism for Bayesian Causal Inference"
---

Two papers of mine were released today on arXiv.

+ [Operator variational inference](https://arxiv.org/abs/1610.09033), in collaboration with Rajesh Ranganath, Jaan Altosaar, and David Blei.
+ [Model criticism for Bayesian causal inference](https://arxiv.org/abs/1610.09037), in collaboration with Francisco Ruiz, Susan Athey, and David Blei.

Last week, I gave a talk at OpenAI on operator variational
inference and Edward. I can now release those
[slides online]({{ site.url }}/talks/Tran_Operator_Edward.pdf).

## Operator variational inference

Operator VI is a paper I'm really excited about. It is at
[NIPS](https://nips.cc) this year. Most directly, it's a continuation of work that
Rajesh and I have been developing on the aim for more expressive
approximations for variational inference.  We've seen this with the
variational Gaussian process {% cite tran2016variational --file
2016-10-30 %} and hierarchical variational models {%
cite ranganath2016hierarchical --file 2016-10-30 %} (and if you've
read my older work, copula variational inference
{% cite tran2015copula --file 2016-10-30 %}).

More generally, in variational inference, we always make tradeoffs
between the statistical efficiency of the approximation and the
computational complexity of the algorithm. (This is partly what Andrew
Gelman calls the "efficiency frontier".) However, we don't quite have
a knob for controlling this tradeoff, nor do we have a way of even
formalizing these notions.

Operator VI is a proposed solution to this problem. It formalizes
these tradeoffs, and it analyzes how we can characterize different
approaches to variational inference in order to achieve specific
aims. As one example, we show how to develop the most expressive
posterior approximations, which we call "variational programs".
Variational programs do not require a tractable density, and they bring
variational inference closer to powerful inferential techniques as in
generative adversarial networks
{% cite goodfellow2014generative --file 2016-10-30 %}.

## Model criticism for Bayesian causal inference

To me, causal inference is one of the most interesting fields in statistics
and machine learning, and with the greatest potential for long term impact.
It can significantly speed up progress towards something like
artificial general intelligence (and is arguably necessary to achieve it). And most immediately, it enables richer
data analyses to capture scientific phenomena. In order for our models
to truly infer generative processes, they must understand and learn
causal notions of the world.

Much of the work in the causal inference community has focused on
nonparametric models, which make few modeling assumptions. They
satisfy theoretic notions such as asymptotics and can perform well on
small-to-medium size data sets (a typical setting setting in applied
causal inference). However, in higher-dimensional and massive data
settings, we require more complex generative models,
as we've seen in probabilistic machine learning.

There's a caveat to this. Before being able to build rich, complex (and possibly deep)
causal models, we first need a way of evaluating them.  This arXiv
paper addresses that issue. It is a foundational question
more generally in the area of model criticism, also known as model
checking and diagnostics. We ask the question, "To what extent
is my model falsified by the empirical data?". By answering it, we can
probe different assumptions in our model and possibly revise them,
thus better capturing causal mechanisms.

References
----------

{% bibliography --cited --file 2016-10-30 %}
