---
layout: post
title: Stochastic Expectation Propagation
---

* Yingzhen Li, Jose Miguel Hernandez-Lobato, and Richard E. Turner. Stochastic Expectation Propagation. In _[Neural Information Processing Systems](http://papers.nips.cc/paper/5760-stochastic-expectation-propagation.pdf)_, 2015.

## Summary

Expectation propagation (EP) is a popular technique for approximate Bayesian inference, although it has arguably lost favor in recent years to variational inference.  Whereas variational inference was made scalable to massive data sets through stochastic variational inference (Hoffman et al., 2013), and thus attractive to machine learning practitioners, expectation propagation is limited by a memory overhead which scales with the number of data points. This paper proposes an extension of EP which removes the complexity requirement and thus enables it to scale to massive data.

Let $\mathbf{x}=\\{\mathbf{x}\_k\\}$ be a data set split into $K$ components, and $p(\mathbf{x}\mid\mathbf{\theta})$ be a model likelihood with prior $p(\mathbf{\theta})$. We aim to learn an approximating distribution $q(\mathbf{\theta})$ such that
$$
p(\mathbf{\theta}\mid \mathbf{x})
\propto
p(\mathbf{\theta})\prod\_{k=1}^K p(\mathbf{x}\_k\mid\mathbf{\theta})
\approx
q(\mathbf{\theta})
\propto
p(\mathbf{\theta})\prod\_{k=1}^K f\_k(\mathbf{\theta}).
$$
Following the way Zoubin Ghahramani motivates EP, we would like to minimize $\operatorname{KL}(p(\mathbf{\theta}\mid \mathbf{x})\|q(\mathbf{\theta}))$ (I have no clue who first motivated EP from this way—Zoubin's slides were how I first learned this). This can be interpreted as a reverse analog of the variational objective: whereas VI is restricted to the support of the posterior and is "mode-seeking" (Minka, 2005), this KL contains _at least_ the support of the posterior and is "inclusive" (Frey, 2000). Minimizing this KL could proceed by iteratively updating the factors $f\_k(\mathbf{\theta})$. Denoting the leave-one-out posterior $p\_{-k}(\mathbf{\theta})=p(\mathbf{\theta}\mid \mathbf{x})/p(\mathbf{x}\_k\mid\mathbf{\theta})$, we iteratively minimize $\operatorname{KL}(p(\mathbf{\theta}\mid \mathbf{x})\|p\_{-k}(\mathbf{\theta})f\_k(\mathbf{\theta}))$ for each $k=1,\ldots,K$.

However, $\operatorname{KL}(p\|q)$ is intractable (and so is the iterative version) as it requires calculating the posterior density; therefore we minimize an approximation to it. Consider approximating the leave-one-out posterior with the _cavity distribution_ $q\_{-k}(\mathbf{\theta}) = q(\mathbf{\theta})/f\_k(\mathbf{\theta})$, which is the approximating distribution without the $k^{th}$ likelihood approximation. Define the _tilted distribution_ $q\_{\backslash k}(\mathbf{\theta})=q\_{-k}(\mathbf{\theta}) p(\mathbf{x}\_k\mid\mathbf{\theta})$, which is the corresponding cavity distribution with the true likelihood factor plugged in.  EP iteratively solves
$$
\min\_{f\_k}\operatorname{KL}(q\_{\backslash k}(\mathbf{\theta}) \| q\_{-k}(\mathbf{\theta}) f\_k(\mathbf{\theta})),\quad k=1,\ldots,K.
$$
This objective is a good proxy to $\operatorname{KL}(p\|q)$ if the true likelihood factor $p(\mathbf{x}\_k\mid\mathbf{\theta})$ contributes to the leave-one-out posterior in the same way as it does in the tilted distribution.

Calculating the cavity distribution for each $k$ requires explicit storage of the approximating factors $f\_k(\mathbf{\theta})$. This implies that EP has an $\mathcal{O}(K)$ memory requirement which prevents it from scaling to large data sets. To solve this, the authors consider storage of only a single factor $f(\mathbf{\theta})$; rather than trying to capture the individual effect of the likelihood factors, the single factor captures only the average effect. Stochastic expectation propagation (SEP) uses the same iterative KL objective to learn the next approximating factor $f\_k(\mathbf{\theta})$, and additionally updates $f(\mathbf{\theta})=f(\mathbf{\theta})^{1-1/K}f\_k(\mathbf{\theta})^{1/K}$.  By storing only the average effect of the likelihood factors, SEP approximates the true cavity distributions $q\_{-k}(\mathbf{\theta})\propto q(\mathbf{\theta})/f\_k(\mathbf{\theta})$ with an averaged cavity distribution $q\_{-1}(\mathbf{\theta})\propto q(\mathbf{\theta})/f(\mathbf{\theta})$.

## Discussion

I'm a big fan of the paper. The exposition on EP, assumed density filtering, and message passing is excellent, and it naturally motivates SEP following the technical developments of EP. The use of an averaged cavity distribution is an unfortunate compromise, but it seems necessary in order to scale local approximation techniques such as EP.

The authors make a cool connection to SVI. If the local minimizations in SEP are instead doing the reverse KL, then this is a form of "stochastic" variational message passing. Moreover, if the update for the single factor $f(\mathbf{\theta})$ follows $f(\mathbf{\theta})^{1 - \epsilon}f\_n(\mathbf{\theta})^{\epsilon}$ where $\epsilon$ uses a Robbins-Monro schedule, then this is the same as stochastic variational inference with a mean-field approximation. An interesting extension then is to apply the equivalent of an adaptive learning rate in stochastic approximations for the geometric scale factor when updating $f(\mathbf{\theta})$.

Hogwild (Nui et al., 2011) and asynchronous versions also naturally follow which is cool. I also think it would have been useful for the paper to fight for the streaming data stance. That is, it's trivial for stochastic EP to learn on streaming data whereas it is impossible for vanilla EP.

The move to $\operatorname{KL}(p\|q)$ with SEP does require some compromises in contrast to SVI unfortunately. For instance, unlike VI to SVI, EP to SEP compromises the approximation quality in order to make it scalable. That is, SVI still performs the same global minimization of $\operatorname{KL}(q\|p)$, and shares nice properties through theory of stochastic approximations (Robbins and Monro, 1951), whereas SEP does not preserve the same local minimization as EP. You can imagine scenarios where an averaged likelihood approximator will not work, or even the "minibatch" M of K extension of them. Additionally, it's not immediately clear how to extend EP beyond approximations which preserve dependence between the local approximations.

Minor comment: like much of Tom Minka's work, they note that the local KL minimizations can more generally use $\alpha$-divergence measures. I still think this is a little restricting, as in general you just want _some_ approximation; therefore something like a Laplace approximation (Smola et al., 2004) is valid as well, and similarly, nested EP (Riihimaki et al., 2013) is more conducive following this motivation.

I'm excited to hear more about stochastic EP from their poster and spotlight at NIPS! (The same authors have a number of interesting extensions of this paper at a few workshops as well.)
