---
layout: post
title: On the paradigms of AI
date: '2015-04-11 10:25:00'
---

There is a recently popular [blog post](http://quantombone.blogspot.com/2015/04/deep-learning-vs-probabilistic.html) which describes what the author claims is the three paradigms in AI: logic, graphical models, and deep learning.

Now, before I put on the skeptic hat, I do think there is some merit in separating the traditional logic-based approaches from empirical methods. The very fact that "traditional AI" does not consider random noise in their problems leads to a fundamentally different solution. Moreover, real life problems often break this zero noise assumption, causing logic methods to be restricted to solving an arguably limited (but useful!) set of tasks.

Of course, however, the further splitting into graphical models and deep learning is both contrived and misleading. On this very matter Yann LeCun stated, ["Deep learning and graphical models are orthogonal concepts that can be (and have been) combined"](https://plus.google.com/wm/1/+YannLeCunPhD/posts/gWE7Jca3Zoq) (it also seems the author is somewhat cognizant of this fact but continues with the categorization regardless).  This combination is also an active area of research in various groups who work on Bayesian (nonparametric) neural nets. The necessity for deep architectures is not surprising either—it's a necessity in order to obtain fine granularity in one's predictions, and furthermore it is irrelevant to the choice of modelling framework.

Thus I believe the division is strictly with

* logic
* empirical methods

One typically considers AI— or what is typically taught in an AI course—as the former, and machine learning as the latter. Machine learning itself is decomposed in various schools of thought:

* neural networks
* kernel methods
* decision trees
* graphical models¹

It is also important to note that this decomposition only addresses the division for supervised learning. There are a variety of other tasks fundamental to machine learning/AI with their own divisions: inference can be broken down in terms of decision theory, computational learning theory, information theory, frequentist statistics, Bayesian, etc.; causality can be broken down in terms of potential outcomes, graphs, and nonparametric structural equation models; the list goes on.

Unfortunately I haven't found a compiled breakdown of all frameworks for AI tasks that I'm satisfied with. The system Wikipedia uses for example is redundant and does not make meaningful categories at times (perceptron is separated from neural network, and decision trees from random forest?). Perhaps someone should make one!

¹ One can consider classic statistical models such as generalized linear models, those incorporating parametric equations from domain knowledge, and Bayesian hierarchical models as instantiations of graphical models.