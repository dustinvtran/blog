---
layout: post
title: Survival analysis, counting processes, and Cox models
---

Survival analysis is the analysis of time duration until the occurrence of an event.  It has a strong root in economics, medicine, engineering, and sociology.

As a statistician, I find most interest in its heavy influence as an application for traditional statistics, where prominent statisticians have placed much effort to develop new methodologies with this domain in mind and many today continue to do so. Survival analysis also has an interesting relationship to counting processes. As the Cox proportional hazards model extends Poisson regression for rates, the Cox process extends the Poisson process. We describe this in detail.

## Survival and hazard functions

Suppose there are $n$ observations $(t\_i, c\_i)$ in which we observe times $t\_1 < t\_2 < \cdots < t\_n$ with corresponding events $c\_i \in \\{0, 1\\}$. The event indicates either that something has occurred, or "failed", ($c\_i=1$) or that the event has not occured, or is "censored" ($c\_i=0$). We call such events censored in the sense that we do not observe the true failure time. For instance, $c\_i=1$ may correspond to the death of a patient and $c\_i=0$ may correspond to the patient dropping out of the study.

The _[survival function](https://en.wikipedia.org/wiki/Survival_analysis#Survival_function)_ is
\begin{equation}
S(t) = Pr(T\ge t)
\end{equation}
which is the probability of failure after time $t$, or equivalently, "surviving" by time $t$. Not surprisingly,  the survival function is often the primary object of interest in survival analysis. The _[hazard function](https://en.wikipedia.org/wiki/Survival_analysis#Hazard_function_and_cumulative_hazard_function)_ is
\begin{equation}
h(t) = \lim\_{dt\to 0} Pr(T < t + dt\mid T\ge t)  = \frac{f(t)}{S(t)}
\end{equation}
Intuitively, the hazard function measures the instantaneous chance of failing at time $t$ given survival up to time $t$. Note that it is neither a probability nor density as it does not integrate to one, c.f., Freedman (2008) for more details.

One interesting property of the hazard function is that one can express the survival function in terms of it:
\begin{align}
h(t) &= \frac{f(t)}{S(t)} = \frac{f(t)}{1 - F(t)} = -\frac{\partial}{\partial t}\log
(1 - F(t)) = -\frac{\partial}{\partial t}\log S(t)
\end{align}
which implies
\begin{equation}
S(t) = \exp\left\\{-\int\_0^{t}h(s)ds\right\\}
\end{equation}
Thus one can write the probability density function $f(t)$ strictly in terms of the hazard function
\begin{equation}
f(t) = h(t) \exp\left\\{-\int\_0^{t}h(s)ds\right\\}
\end{equation}
Therefore for statistical analysis of any form—causal, experimental design, prediction, etc—one would like to characterize the hazard function. The Cox proportional hazards model (Cox, 1972) is the most popular method for doing so. Another is the [accelerated failure time model](https://en.wikipedia.org/wiki/Accelerated_failure_time_model), which models the effect of covariates as accelerating or decelerating the life of an event, c.f., Cox and Oates (1984) for a description.

## Cox proportional hazards model and Poisson regression
Let $h\_0(t)$ denote the _baseline hazard function_, and let $x\_i \in \mathbf{R}^p$ be covariates for observation $i$. The _[Cox proportional hazards model](https://en.wikipedia.org/wiki/Proportional_hazards_model)_ specifies the hazard function for the $i^{th}$ observation as
\begin{equation}
h(t\mid \theta, x\_i) = h\_0(t)\exp(x\_i'\theta)
\end{equation}
where the parameters of interest are $\theta$ and the baseline hazard $h\_0(\cdot)$ is unknown. The model is semiparametric: the covariates are multiplicatively related to the baseline—thus having a parametric form—but the model  does not require any specification of the baseline hazard function—thus having a nonparametric form.

What makes this model so powerful is in its ease of inference while being semiparametric. It is possible to construct a partial likelihood, which allows one to estimate the model parameters $\theta$, without any consideration of the functional form of the baseline hazard itself. To do so, first note that for a censored observation $(t\_i, c\_i=0)$, we only know that its lifetime exceeds $t\_i$ and thus its individual likelihood is $p(t\_i,c\_i=0\mid\theta,x\_i)=S(t\_i\mid\theta,x\_i)$. Then the likelihood of all data points can be written as
\begin{align}
L(\theta\mid \mathbf{t},\mathbf{c},\mathbf{x})
&= \prod\_{i=1}^n h(t\_i\mid \theta, x\_i)^{c\_i} S(t\_i\mid \theta, x\_i)
\\\\
&= \prod\_{i=1}^n (h\_0(t\_i)\exp(x\_i'\theta))^{c\_i} \exp\left\\{-{\exp(x\_i'\theta)}\int\_0^{t\_i}
h\_0(s)ds\right\\}
\end{align}
The log-likelihood is thus
\begin{align}
\ell(\theta; \mathbf{t},\mathbf{c},\mathbf{x}) = \sum\_{i=1}^n \left\[c\_i \log
h\_i + c\_ix\_i'\theta - \exp(x\_i'\theta) H\_i\right\]
\end{align}
where $h\_i=h\_0(t\_i)$ and $H\_i = \sum\_{j=1}^i h\_j$. Thus the score function is
\begin{align}
\nabla \ell(\theta; \mathbf{t},\mathbf{c},\mathbf{x}) = \sum\_{i=1}^n\left[c\_i x\_i - H\_i \exp(x\_i'\theta) x\_i\right] =
\sum\_{i=1}^n (c\_i - H\_i \exp(x\_i'\theta)) x\_i,
\end{align}
Note that the baseline hazard evaluations $h\_i$ which form $H\_i$ are unknown.  However, one can show that their MLE given $\theta$ is
\begin{equation}
\widehat{h\_i}=  \frac{c\_i}{\sum\_{j \in \mathcal{R}\_i}\exp(x\_j'\theta)}
\end{equation}
where $\mathcal{R}\_i$ is the _risk set_, i.e., the set of units available to fail. In this case, $\mathcal{R}\_i = \\{i, i+1, \ldots n\\}$ by our ordering assumption.

One can also derive the Hessian, but a simple maximum likelihood procedure using gradient descent can now easily proceed: one iterates $\theta\_n$, re-estimates $\hat h\_i$'s conditional on the new update of $\theta\_n$, then repeats.

Perhaps surprisingly, estimates from this partial likelihood, which require no estimation of the baseline hazard $h_0(\cdot)$, share the same asymptotic properties as that of the full likelihood (Tsiatis, 1975).

## Poisson models and stochastic processes
On a modelling perspective, the Cox proportional hazards model has a striking resemblance to Poisson regression. If the baseline hazard is a constant $h\_0(t)=\lambda$ for some $\lambda\in\mathbb{R}$, then the log likelihood is
\begin{align}
\ell(\theta; \mathbf{t},\mathbf{c},\mathbf{x})
&= \sum\_{i=1}^n \left\[c\_i \log
h(t\_i\mid\theta,x\_i) + \log S(t\_i\mid\theta,x\_i)\right\]
\\\\
&= \sum\_{i=1}^n \left\[c\_i \log
[\lambda\exp(x\_i'\theta)] - t\_i\lambda\exp(x\_i'\theta)\right\]
\end{align}
Excluding additive constants, this is the same expression as the log likelihood of the $c\_i$'s, seen as realizations $c\_i\mid t\_i,x\_i\sim\operatorname{Poisson}(\mu\_i)$, where $\mu\_i=t\_i\lambda\exp(x\_i'\theta)$. Therefore the Cox proportional hazards model with constant baseline $\lambda$ is equivalent to the Poisson regression
\begin{equation}
\log\mathbb{E}[c\_i\mid t\_i,x\_i] =
\log t\_i + \log\lambda + x\_i'\theta
\end{equation}
where one can interpret $\log t\_i$ as an offset for modelling the rate $\log (\mathbb{E}[c\_i\mid t\_i,x\_i]/t\_i)$, and $\log\lambda$ as an intercept. In full generality, the Cox model is simply a Poisson model for rates in which the "intercept" $\log h_0(t\_i)$ is a function varying over time.

If one has prior knowledge about the structural form  of the baseline hazard, then it makes sense to forgo the partial likelihood approach and to adopt a Bayesian methodology, in which one places a stochastic process as a prior for $h\_0(t)$, e.g., using a Gaussian process, Beta process, or extended Gamma process, see Dey and Rao (2005; Section 3.2.1) for more details.

The specification of the hazard function in survival analysis is analogous to specifying the intensity function for point processes. That is, as the Cox proportional hazards model with constant baseline hazard $\lambda$ is equivalent to a form of Poisson regression, a Cox process with constant intensity $\lambda$ is equivalent to the Poisson process.

Another interesting relationship is that in survival analysis, the estimand of interest is often not a count but a rate. After all, a count of 1 already implies that the person is dead! There are more general formulations of survival analysis which consider multiple failures for a single unit, however, and the special case of just one already has much significant analysis. How this might tie into current progress on Bayesian nonparametrics for count data is unknown and certainly worth looking into.

### References
* David R. Cox. Regression models and life tables (with discussion). _Journal of the Royal Statistical Society: Series B_, 34:187-220, 1972.
* David R. Cox. Partial likelihood. _Biometrika_, 62: 269-276, 1975.
* David R. Cox and David Oakes. _Analysis of Survival Data_. CRC Press, 1984.
* Dipak Dey and Calyampudi Radhakrishna Rao. _Bayesian Thinking: Modeling and Computation_. North Holland, 2005.
* David A. Freedman. Survival analysis: a primer. Technical Report, 2008.
* Anastasios Tsiatis. A Large Sample Study of Cox's Regression Model. _Annals of Statistics_, 9(1): 93-108, 1981.
