---
layout: post
title: Reflections on a data science internship
---

![]({{ site.baseurl }}/assets/2014-08-15-figure.jpg)

Over the summer I had the pleasure of working for [Earnest](https://www.meetearnest.com/), a financial technology startup which offers personal loans using a merit-based algorithmic system. The experience was educational, challenging, and—simply put—fun. I'm glad that of all the summer offers I was deciding upon at the time, I chose  Earnest.

Here I'll pinpoint some of the amazing things that really attracted me about the startup, as well as how I feel about the time I spent working there. You can read about what I actually did [here](http://not.a.website) (to be published: pending company approval for super secret algorithm reasons).

# Impact

One of the best aspects about the internship is that you're treated as a full-time employee. This is not only reflected in how others treat you, but also in the level of responsibility you have in the company. As an intern, I was able to build our primary predictive algorithms from scratch. Of course, the lead data scientist [Austen](https://twitter.com/austenhead) was always around if I had any questions or tips to approach certain challenges, but I was given full responsibility for writing the algorithms.

There are also advantages common to early stage startups in general: you work across multiple teams and interfaces of the product, you face multiple challenging problems over the period, and, perhaps most importantly, **you actually get things done**. I've heard too many intern stories about how they've worked on minor projects that were insubstantial to the company itself, oftentimes microoptimizing some functionality because they couldn't be trusted to do anything more. At an early stage startup, there's so much work left to do that you're bound to find one which is beneficial for both you in terms of your interests and the company in terms of its future.

# Data
Data science is a still-evolving field, and it's difficult to find the best fit because the job description for data scientists varies drastically across companies. Many companies and job-seekers alike do not understand the role of data scientists, positioning them/themselves too often as classical analysts, consultants, or technicians.

Data science is by nature a cross-disciplinary field, incorporating classical theory and inference from statistics, as well as algorithms and programming expertise from computer science. As data scientists, we need to be able to be strong in both areas—even a third if one includes the mathematics necessary for the theory and development behind the algorithms: proofs, convergence rates, convexity, generalizations and constraints, etc.

Fortunately, I was able to use the most of my abilities during my internship. I would query from a database like MongoDB or PostgreSQL, preprocess text in Python, do some exploratory data analysis and prototyping in R, tune parameters for optimization, and make inferences from our models and assumptions.

<!--
Coding-wise, I improved my knowledge of awesome packages in R like `ggplot2`, `googleVis`, `plyr`, and `knitr`, and modules in Python like `pandas`, `scipy`, `scikit-learn`, and how our results in the modeling side and metric reporting would be applied in other teams such as marketing and underwriting/risk.
-->

# Culture

The talented (and dedicated) people at Earnest make the place a joy to work. We value transparency to the point where everyone receives everyone else's emails, can follow other people's calendars, and can join and listen to any of the detailed weekly reviews going on. Earnest is open and friendly enough that a person can bring up a project they're working on and get feedback from others regardless of assigned responsibilities or teams.

We also have weekly *Team Nights* (formerly called *Jank n Drank*, and pending a more creative name!), where every Wednesday we would spend time together playing games, eating dinner, and having a few laughs. Settlers of Catan can get quite competitive here, which may not be surprising given all the computer geeks and math nerds, and also that our [CEO](https://twitter.com/louisberyl) used to play Magic the Gathering competitively.

*Lunch n Learns* feature a guest speaker with fountains of industry knowledge and who talks about his or her personal experiences. The atmosphere is always laid back and inviting: munching, talking, joking, and chiming in questions as interesting ideas arise. This has personally been my favorite event because of all the insights provided by these amazingly talented people. We've had speakers from Coinbase, Lyft, Facebook, and Dropbox, and we're always incredibly excited to have them volunteer their time to come here and speak.


# Pace

Earnest is lean, reactive, quick to ship, and scalable. There's a reason it received [$15 million](http://www.crunchbase.com/organization/earnest) in venture funding from investors such as Andreessen Horowitz and First Round Capital. Edmond Lau on Quora describes this best for early stage startups in general:
> "Pre-commit code reviews, weekly, biweekly, or even monthly release cycles, launch checklists, and formalized product approval meetings are all mechanisms and processes aimed at creating structure and minimizing breakages at larger companies, but often at the cost of development speed. Continuous deployment (where every commit can go to production), post-commit code reviews, and lighter-weight approval processes at startups aim to allow for quick iteration while providing for basic quality control." [[1]](http://qr.ae/MQD17)

There is no sluggish legacy code or trying to get our products to work from a long history, so we're able to use the latest tools in the industry. We're quick to adapt and willing to try new techniques if they can better ourselves in the long run. After all, the end goal is not to optimize for short term returns but for long term value. We lend personal loans to people now and years later we may even do their home mortgage.


# Advice

Here are a quick few pointers for future data scientists and interns, not only at Earnest but for jobs in general:

* Stay up to speed on every ongoing project.
* Go to networking events.
* Take advantage of the open-endedness of your work.
* Don't be afraid of attempting novel approaches. (It could lead to more interesting problems to solve!)
* Spend time finding precisely what you'd like out of the period you'll be working, and voice your opinions regarding them.
