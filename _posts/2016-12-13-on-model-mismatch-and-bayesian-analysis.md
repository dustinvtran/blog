---
layout: post
title: On Model Mismatch and Bayesian Analysis
---

One aspect I always enjoy about machine learning is that questions
often go back to the basics. The field essentially goes into an
existential crisis every dozen years---rethinking our tools and asking foundational questions
such as "why neural networks" or "why generative models".<sup>1</sup>

This was a theme in my conversations during
[NIPS 2016](https://nips.cc/Conferences/2016) last week, where a
frequent topic was
on the advantages of a Bayesian perspective to machine learning.
Not surprisingly, this appeared as a big discussion point during the
panel at the [Bayesian deep learning
workshop](http://bayesiandeeplearning.org), where many
panelists were conciliatory to the use of non-Bayesian approaches.
(Granted, much of it was Neil trolling them to admit when non-Bayesian
approaches worked better in practice.)

One argument against Bayesian analysis went as follows:

> While Bayesian inference can capture uncertainty about parameters,
> it relies on the model being correctly specified. However, in
> practice, all models are wrong. And in fact, this model mismatch can
> be often be large enough that we should be more concerned with
> calibrating our inferences to correct for the mismatch than to
> produce uncertainty estimates from incorrect assumptions.

A related complaint was on the separation of model and
inference, a philosophical point commonly associated with Bayesians:

> While in principle it is nice that we can build models separate from
> our choice of inference, we often need to combine the two in practice. (The whole
> naming behind the popular model-inference classes of "variational
> auto-encoders" {% cite kingma2014autoencoding --file 2016-12-13 %}
and "generative adversarial networks" {% cite goodfellow2014generative
--file 2016-12-13 %} are one
> example.) That is, we often choose our model based on what we know
> enables fast inferences, or we select hyperparameters in our model
> from data. This goes against the Bayesian paradigm.

First, I'd like to say immediately that I think interpreting Bayesian
analysis as a two-step procedure of setting up a probability model,
then performing posterior inference is outdated. Certainly this was the
prevailing perspective back in the 80s' and 90s' when Markov chain Monte Carlo
was first popularized, and when statisticians started to take Bayesian analysis
more seriously {% cite robert2011short --file 2016-12-13 %}.

Quoting {% cite gelman2012philosophy --style apa-text --file 2016-12-13 %}
who summarize this perspective,
"The expression $$p(\theta\mid y)$$ says it all, and the central goal of Bayesian inference is computing the posterior probabilities of hypotheses. Anything not contained in the posterior distribution $$p(\theta\mid y)$$ is simply irrelevant, and it would be irrational (or incoherent) to attempt falsification, unless that somehow shows up in the posterior."

__Like many statisticians before me__
(e.g., {% cite box1980sampling --style apa-text --file 2016-12-13 %},
{% cite good1983good --style apa-text --file 2016-12-13 %},
{% cite rubin1984bayesianly --style apa-text --file 2016-12-13 %},
{% cite jaynes2003probability --style apa-text --file 2016-12-13 %}),
__I believe this perspective is wrong. Bayesian analysis is no
different in its testing and falsification of models than any other
inferential paradigm__
({% cite fisher1925statistical --style apa-text --file 2016-12-13 %},
{% cite neyman1933on --style apa-text --file 2016-12-13 %}).

An important third step to all empirical analyses is _model criticism_
({% cite box1980sampling --style apa-text --file 2016-12-13 %},
{% cite ohagan2011hsss --style apa-text --file 2016-12-13 %}
),
also known as model
validation, or model
checking and diagnostics
({% cite rubin1984bayesianly --style apa-text --file 2016-12-13 %},
{% cite meng1994posterior --style apa-text --file 2016-12-13 %},
{% cite gelman1996posterior --style apa-text --file 2016-12-13 %}).
In criticizing our models after inference, we can either justify
use of the model or find directions in which we can revise the model.
By revising the model, we go back to the modeling step, thus forming
a loop, called _Box's loop_
({% cite box1976science --style apa-text --file 2016-12-13 %},
{% cite blei2014build --style apa-text --file 2016-12-13 %},
{% cite gelman2013bayesian --style apa-text --file 2016-12-13 %}).
<sup>2</sup>

From my perspective, this solves the perceived problem of conflating
model and inference, whether it be to address model mismatch or to
build the model from previous inferences or data.
That is, while
posterior inference is simply a mechanical step of calculating a
conditional distribution, the
step of model criticism is about the relevance of the model to future
data---to put it in statistical terms, the relevance of the model with
respect to a population distribution
{% cite wasserman2006frequentist --file 2016-12-13 %}.
As with data, the model is
just a source of information, and posterior inference simply aggregates these
two sources of information. Thus it
makes sense that as we better understand properties of the data, we
can revise our information to better formulate a model of it
{% cite tukey1977exploratory --file 2016-12-13 %}.

This might sound like an awkward way to shoehorn Bayesian analysis to
mimick frequentist properties, or no different from combining model
and inference from the get-go.
However, this loop is fundamental because it still emphasizes the
importance of separating the two.  We can continue to form
hypothetico-deductive analyses---namely, a falsificationist view of
the world where components of model, inference, and criticism
interact---while still incorporating posterior probabilities.

For more details, I highly recommend
{% cite gelman2012philosophy --style apa-text --file 2016-12-13 %}
and of course the classic,
{% cite rubin1984bayesianly --style apa-text --file 2016-12-13 %}.

<sup>1</sup>
I take an optimistic viewpoint to the trend of cycling among tools for
machine learning. The trend is based on what works best empirically,
and I think that's important.

<sup>2</sup>
As a plug, I should also mention that this is what [Edward](http://edwardlib.org) is all about.

References
----------

{% bibliography --cited --file 2016-12-13 %}
