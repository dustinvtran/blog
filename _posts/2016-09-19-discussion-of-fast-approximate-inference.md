---
layout: post
title: Discussion of "Fast Approximate Inference for Arbitrarily Large Semiparametric Regression Models via Message Passing"
author: <a class="author-name" href="{{ site.url }}">Dustin Tran</a> and <a class="author-name" href="http://www.cs.columbia.edu/~blei/">David Blei</a>
---

_This article is written with much help by David Blei. It is extracted from a discussion paper on "Fast Approximate Inference for Arbitrarily Large Semiparametric Regression Models via Message Passing"._ [[link]](http://arxiv.org/abs/1609.05615)

We commend {% cite wand2016fast --style apa-text --file 2016-09-19 %} for an excellent description of
message passing (<span style="font-variant:small-caps;">mp</span>) and for developing it to infer large semiparametric
regression models.  We agree with the author in fully embracing the
modular nature of message passing, where one can define "fragments"
that enable us to compose localized algorithms.  We believe this
perspective can aid in the development of new algorithms for automated
inference.

__Automated inference.__  The promise of automated algorithms is
that modeling and inference can be separated. A user can construct
large, complicated models in accordance with the assumptions he or she
is willing to make about their data. Then the user can use generic
inference algorithms as a computational backend in a "probabilistic
programming language," i.e., a language for specifying generative
probability models.

With probabilistic programming, the user no longer has to write their
own algorithms, which may require tedious model-specific derivations
and implementations. In the same spirit, the user no longer has to
bottleneck their modeling choices in order to fit the requirements of
an existing model-specific algorithm.  Automated inference enables
probabilistic programming systems, such as
Stan {% cite carpenter2016stan --file 2016-09-19 %}, through methods like
automatic differentiation variational inference (<span style="font-variant:small-caps;">advi</span>) {% cite kucukelbir2016automatic --file 2016-09-19 %} and
no U-turn sampler (<span style="font-variant:small-caps;">nuts</span>) {% cite hoffman2014nuts --file 2016-09-19 %}.

Though they aim to apply to a large class of models, automated
inference algorithms typically need to incorporate modeling structure
in order to remain practical. For example, Stan assumes that one can
at least take gradients of a model's joint density. (Contrast this
with other languages which assume one can only sample from the model.)
However, more structure is often necessary: <span style="font-variant:small-caps;">advi</span> and <span style="font-variant:small-caps;">nuts</span>
are not fast enough by themselves to infer very large models, such as
hierarchical models with many groups.

We believe <span style="font-variant:small-caps;">mp</span> and Wand's work could offer fruitful avenues for
expanding the frontiers of automated inference. From our perspective,
a core principle underlying <span style="font-variant:small-caps;">mp</span> is to leverage structure when it
is available---in particular, statistical properties in the model---which provides useful computational properties.  In <span style="font-variant:small-caps;">mp</span>, two
examples are conditional independence and conditional conjugacy.

__From conditional independence to distributed computation.__
As {% cite wand2016fast --style apa-text --file 2016-09-19 %} indicates, a crucial advantage of message
passing is that it modularizes inference; the computation can be
performed separately over conditionally independent posterior
factors. By definition, conditional independence separates a posterior
factor from the rest of the model, which enables <span style="font-variant:small-caps;">mp</span> to define a
series of iterative updates. These updates can be run asynchronously
and in a distributed environment.

<center>
<img src="{{site.baseurl}}/assets/2016-09-19-figure.png" style="width:200px;"/>
</center>
_Figure 1.
A hierarchical model, with latent variables $$\alpha_k$$ defined locally
per group and latent variables $$\phi$$ defined globally to be shared across groups._

We are motivated by hierarchical models, which substantially benefit
from this property. Formally, let $$y_{nk}$$ be the $$n^{th}$$ data
point in group $$k$$, with a total of $$N_k$$ data points in group $$k$$ and
$$K$$ many groups. We model the data using local latent variables
$$\alpha_k$$ associated to a group $$k$$, and using global latent
variables $$\phi$$ which are shared across groups. The model is depicted
in Figure 1.

The posterior distribution of local variables $$\alpha_k$$ and global
variables $$\phi$$ is

$$
p(\alpha,\phi\mid\mathbf{y}) \propto
p(\phi\mid\mathbf{y}) \prod_{k=1}^K
\Big[ p(\alpha_k\mid \beta) \prod_{n=1}^{N_K} p(y_{nk}\mid\alpha_k,\phi) \Big].
$$

The benefit of distributed updates over the independent factors is
immediate. For example, suppose the data consists of 1,000 data points
per group (with 5,000 groups); we model it with 2 latent variables per
group and 20 global latent variables.  Passing messages, or
inferential updates, in parallel provides an attractive approach to
handling all 10,020 latent dimensions. (In contrast, consider a
sequential algorithm that requires taking 10,019 steps for all other
variables before repeating an update of the first.)

While this approach to leveraging conditional independence is
straightforward from the message passing perspective, it is not
necessarily immediate from other perspectives.  For example, the
statistics literature has only recently come to similar ideas,
motivated by scaling up Markov chain Monte Carlo using divide and
conquer strategies {% cite huang2005sampling wang2013parallelizing --file 2016-09-19 %}.
These first analyze data locally over a partition of the joint
density, and second aggregate the local inferences.  In our work in
{% cite gelman2014expectation --style apa-text --file 2016-09-19 %}, we arrive at the continuation of this
idea. Like message passing, the process is iterated, so that local
information propagates to global information and global information
propagates to local information. In doing so, we obtain a scalable
approach to Monte Carlo inference, both from a top-down view which
deals with fitting statistical models to large data sets and from a
bottom-up view which deals with combining information across local
sources of data and models.

__From conditional conjugacy to exact iterative updates.__
Another important element of message passing algorithms is conditional
conjugacy, which lets us easily calculate the exact distribution for a
posterior factor conditional on other latent variables. This enables
analytically tractable messages (c.f., Equations (7)-(8) of
{% cite wand2016fast --style apa-text --file 2016-09-19 %}).

Consider the same hierarchical model discussed above, and set

$$
p(y_k,\alpha_k\mid \phi)
= h(y_k, \alpha_k) \exp\{\phi^\top t(y_k, \alpha_k) - a(\phi)\},
$$

$$
p(\phi)
= h(\phi) \exp\{\eta^{(0) \top} t(\phi) - a(\eta_0)\}
.
$$

The local factor $$p(y_k,\alpha_k\mid\phi)$$ has sufficient statistics
$$t(y_k,\alpha_k)$$ and natural parameters given by the global latent
variable $$\phi$$.  The global factor $$p(\phi)$$ has sufficient
statistics $$t(\phi) = (\phi, -a(\phi))$$, and with fixed
hyperparameters $$\eta^{(0)}$$, which has two components: $$\eta^{(0)} =
(\eta^{(0)}_1,\eta^{(0)}_2)$$.

This exponential family structure implies that, conditionally, the
posterior factors are also in the same exponential families
as the prior factors {% cite diaconis1979conjugate --file 2016-09-19 %},

$$
p(\phi\mid\mathbf{y},\alpha)
= h(\phi) \exp\{\eta(\mathbf{y},\alpha)^\top t(\phi) - a(\mathbf{y},\alpha)\},
$$

$$
p(\alpha_k\mid y_k, \phi)
= h(\alpha_k) \exp\{\eta(y_k, \phi)^\top t(\alpha_k) - a(y_k, \phi)\}
.
$$

The global factor's natural parameter is $$\eta(\mathbf{y},\alpha) =
(\eta^{(0)}_1 + \sum_{k=1}^K t(y_k, \alpha_k), \eta^{(0)}_2 + \sum_{k=1}^K N_k)$$.

With this statistical property at play---namely that conjugacy gives
rise to tractable conditional posterior factors---we can derive
algorithms at a conditional level with exact iterative updates.  This
is assumed for most of the message passing of semiparametric models in
{% cite wand2016fast --style apa-text --file 2016-09-19 %}.  Importantly, this is not necessarily a
limitation of the algorithm. It is a testament to leveraging model
structure: without access to tractable conditional posteriors,
additional approximations must be made.  {% cite wand2016fast --style apa-text --file 2016-09-19 %} provides
an elegant way to separate out these nonconjugate pieces from the
conjugate pieces.

In statistics, the most well-known example which leverages
conditionally conjugate factors is the Gibbs sampling algorithm.  From
our own work, we apply the idea in order to access fast natural
gradients in variational inference, which accounts for the information
geometry of the parameter space {% cite hoffman2013stochastic --file 2016-09-19 %}.  In
other work, we demonstrate a collection of methods for gradient-based
marginal optimization {% cite tran2016gradient --file 2016-09-19 %}.  Assuming forms of
conjugacy in the model class arrives at the classic idea of
iteratively reweighted least squares as well as the EM algorithm. Such
structure in the model provides efficient algorithms---both
statistically and computationally---for their automated inference.

__Open Challenges and Future Directions.__ Message passing is a
classic algorithm in the computer science literature, which is ripe
with interesting ideas for statistical inference. In particular,
<span style="font-variant:small-caps;">mp</span> enables new advancements in the realm of automated inference,
where one can take advantage of statistical structure in the model.
{% cite wand2016fast --style apa-text --file 2016-09-19 %} makes great steps following this direction.

With that said, important open challenges still exist in order to
realize this fusion.

First is about the design and implementation of probabilistic
programming languages. In order to implement {% cite wand2016fast --style apa-text --file 2016-09-19 %}'s
message passing, the language must provide ways of identifying local
structure in a probabilistic program.  While that is enough to let
practitioners use <span style="font-variant:small-caps;">mp</span>, a much larger challenge is to
then automate the process of detecting local structure.

Second is about the design and implementation of inference engines.
The inference must be extensible, so that users can not only employ
the algorithm in {% cite wand2016fast --style apa-text --file 2016-09-19 %} but easily build on top of it.
Further, its infrastructure must be able to encompass a variety of
algorithms, so that users can incorporate <span style="font-variant:small-caps;">mp</span> as one of many
tools in their toolbox.

Third, we think there are innovations to be made on taking the stance
of modularity to a further extreme. In principle, one can compose not
only localized message passing updates but compose localized inference
algorithms of any choice---whether it be exact inference, Monte Carlo,
or variational methods.  This modularity will enable new
experimentation with inference hybrids and can bridge the gap among
inference methods.

Finally, while we discuss <span style="font-variant:small-caps;">mp</span> in the context of automation,
fully automatic algorithms are not possible. Associated to all
inference are statistical and computational
tradeoffs {% cite jordan2013statistics --file 2016-09-19 %}.  Thus we need algorithms along
the frontier, where a user can explicitly define a computational
budget and employ an algorithm achieving the best statistical
properties within that budget; or conversely, define desired
statistical properties and employ the fastest algorithm to achieve
them.  We think ideas in <span style="font-variant:small-caps;">mp</span> will also help in developing some of
these algorithms.

References
----------

{% bibliography --cited --file 2016-09-19 %}
