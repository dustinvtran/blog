---
layout: post
title: My Qualifying Exam (Oral)
---

I'm taking my qualifying exam this Tuesday—which may surprise some of
you that I haven't already done it! This is mostly due to logistical
kerfuffles as I transferred Ph.D.'s and I also tend to avoid coursework
like the plague.

Each university has its own culture around an oral or qualifying exam.
Columbia's Computer Science department involves the following:

> The committee, after consideration of the student’s input, selects a syllabus of the 20-30 most significant documents that encompass the state of the art in the area. [...] The oral exam begins with the student’s 30 minute critical evaluation of the syllabus materials, and is followed by no more than 90 minutes of questioning by the committee on any subject matter related to their contents. The student is judged primarily on the oral evidence, but the content and style of the presentation can account for part of the decision.
[[url]](http://www.cs.columbia.edu/education/phd/requirements/candidacy/)

My syllabus concerns _Bayesian deep learning_, which is the
synthesis of modern Bayesian analysis with deep learning.
The syllabus includes 29 papers published in 2014 or later,
representing "the most significant documents that encompass the
state of the art in the area."
I got multiple requests from friends to share the list, so I decided
to just share it publically.

__Probabilistic programming & AI systems__
1. {% cite mansinghka2014venture --style apa-text --file 2017-08-07 %}
2. {% cite tristan2014augur --style apa-text --file 2017-08-07 %}
3. {% cite schulman2015gradient --style apa-text --file 2017-08-07 %}
4. {% cite narayanan2016probabilistic --style apa-text --file 2017-08-07 %}
5. {% cite abadi2016tensorflow --style apa-text --file 2017-08-07 %}
6. {% cite carpenter2016stan --style apa-text --file 2017-08-07 %}
7. {% cite tran2016edward --style apa-text --file 2017-08-07 %}
8. {% cite kucukelbir2017automatic --style apa-text --file 2017-08-07 %}
9. {% cite tran2017deep --style apa-text --file 2017-08-07 %}
10. {% cite neubig2017dynet --style apa-text --file 2017-08-07 %}

__Variational inference__
1. {% cite kingma2014autoencoding --style apa-text --file 2017-08-07 %}
3. {% cite ranganath2014black --style apa-text --file 2017-08-07 %}
2. {% cite rezende2014stochastic --style apa-text --file 2017-08-07 %}
4. {% cite mnih2014neural --style apa-text --file 2017-08-07 %}
5. {% cite rezende2015variational --style apa-text --file 2017-08-07 %}
6. {% cite salimans2015markov --style apa-text --file 2017-08-07 %}
7. {% cite tran2016variational --style apa-text --file 2017-08-07 %}
8. {% cite ranganath2016hierarchical --style apa-text --file 2017-08-07 %}
9. {% cite maaloe2016auxiliary --style apa-text --file 2017-08-07 %}
10. {% cite johnson2016composing --style apa-text --file 2017-08-07 %}
11. {% cite ranganath2016operator --style apa-text --file 2017-08-07 %}
12. {% cite gelman2017expectation --style apa-text --file 2017-08-07 %}

__Implicit probabilistic models & adversarial training__
1. {% cite goodfellow2014generative --style apa-text --file 2017-08-07 %}
2. {% cite dziugaite2015training --style apa-text --file 2017-08-07 %}
3. {% cite li2015generative --style apa-text --file 2017-08-07 %}
4. {% cite radford2016unsupervised --style apa-text --file 2017-08-07 %}
5. {% cite mohamed2016learning --style apa-text --file 2017-08-07 %}
6. {% cite arjovsky2017wasserstein --style apa-text --file 2017-08-07 %}
7. {% cite tran2017deepand --style apa-text --file 2017-08-07 %}

Committee: David Blei, Andrew Gelman, Daniel Hsu.

Disclaimer: I favored papers which have
shown to be or are most likely to be long-lasting in influence (this
means fewer papers from 2017); papers on methodology rather than
applications (only to narrow the scope); original papers over surveys;
and my own papers (because it's my oral). If I did not cite you or if
you have strong opinions about a missing paper, recall [Hanlon's
razor](https://en.wikipedia.org/wiki/Hanlon%27s_razor). E-mail me your
suggestions.

References
----------

{% bibliography --cited --file 2017-08-07 %}
