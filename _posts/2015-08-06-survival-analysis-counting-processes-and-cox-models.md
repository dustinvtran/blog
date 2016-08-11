---
layout: post
title: Survival analysis, counting processes, and Cox models
---

Survival analysis is the analysis of time duration until the occurrence of an event.  It has a strong root in economics, medicine, engineering, and sociology.

As a statistician, I find most interest in its heavy influence as an application for traditional statistics, where prominent statisticians have placed much effort to develop new methodologies with this domain in mind and many today continue to do so. Survival analysis also has an interesting relationship to counting processes. As the Cox proportional hazards model extends Poisson regression for rates, the Cox process extends the Poisson process. We describe this in detail.

## Survival and hazard functions

Suppose there are $$n$$ observations $$(t_i, c_i)$$ in which we observe times $$t_1 \prec t_2 \prec \cdots \prec t_n$$ with corresponding events $$c_i \in \{0, 1\}$$. The event indicates either that something has occurred, or "failed", ($$c_i=1$$) or that the event has not occured, or is "censored" ($$c_i=0$$). We call such events censored in the sense that we do not observe the true failure time. For instance, $$c_i=1$$ may correspond to the death of a patient and $$c_i=0$$ may correspond to the patient dropping out of the study.

The _[survival function](https://en.wikipedia.org/wiki/Survival_analysis#Survival_function)_ is

$$
S(t) = Pr(T\ge t)
$$

which is the probability of failure after time $$t$$, or equivalently, "surviving" by time $$t$$. Not surprisingly,  the survival function is often the primary object of interest in survival analysis. The _[hazard function](https://en.wikipedia.org/wiki/Survival_analysis#Hazard_function_and_cumulative_hazard_function)_ is

$$
h(t) = \lim_{dt\to 0} Pr(T \prec t + dt\mid T\ge t)  = \frac{f(t)}{S(t)}
$$

Intuitively, the hazard function measures the instantaneous chance of failing at time $$t$$ given survival up to time $$t$$. Note that it is neither a probability nor density as it does not integrate to one, c.f., Freedman (2008) for more details.

One interesting property of the hazard function is that one can express the survival function in terms of it:
$$
h(t) = \frac{f(t)}{S(t)} = \frac{f(t)}{1 - F(t)} = -\frac{\partial}{\partial t}\log
(1 - F(t)) = -\frac{\partial}{\partial t}\log S(t)
$$

which implies

$$
S(t) = \exp\left\{-\int_0^{t}h(s)ds\right\}
$$
Thus one can write the probability density function $$f(t)$$ strictly in terms of the hazard function

$$
f(t) = h(t) \exp\left\{-\int_0^{t}h(s)ds\right\}
$$

Therefore for statistical analysis of any form—causal, experimental design, prediction, etc—one would like to characterize the hazard function. The Cox proportional hazards model (Cox, 1972) is the most popular method for doing so. Another is the [accelerated failure time model](https://en.wikipedia.org/wiki/Accelerated_failure_time_model), which models the effect of covariates as accelerating or decelerating the life of an event, c.f., Cox and Oates (1984) for a description.

## Cox proportional hazards model and Poisson regression
Let $$h_0(t)$$ denote the _baseline hazard function_, and let $$x_i \in \mathbf{R}^p$$ be covariates for observation $$i$$. The _[Cox proportional hazards model](https://en.wikipedia.org/wiki/Proportional_hazards_model)_ specifies the hazard function for the $$i^{th}$$ observation as

$$
h(t\mid \theta, x_i) = h_0(t)\exp(x_i'\theta)
$$

where the parameters of interest are $$\theta$$ and the baseline hazard $$h_0(\cdot)$$ is unknown. The model is semiparametric: the covariates are multiplicatively related to the baseline—thus having a parametric form—but the model  does not require any specification of the baseline hazard function—thus having a nonparametric form.

What makes this model so powerful is in its ease of inference while being semiparametric. It is possible to construct a partial likelihood, which allows one to estimate the model parameters $$\theta$$, without any consideration of the functional form of the baseline hazard itself. To do so, first note that for a censored observation $$(t_i, c_i=0)$$, we only know that its lifetime exceeds $$t_i$$ and thus its individual likelihood is $$p(t_i,c_i=0\mid\theta,x_i)=S(t_i\mid\theta,x_i)$$. Then the likelihood of all data points can be written as

```
\begin{align}
L(\theta\mid \mathbf{t},\mathbf{c},\mathbf{x})
&= \prod_{i=1}^n h(t_i\mid \theta, x_i)^{c_i} S(t_i\mid \theta, x_i)
\\
&= \prod_{i=1}^n (h_0(t_i)\exp(x_i'\theta))^{c_i} \exp\left\{-{\exp(x_i'\theta)}\int_0^{t_i}
h_0(s)ds\right\}
\end{align}
```

The log-likelihood is thus

$$
\ell(\theta; \mathbf{t},\mathbf{c},\mathbf{x}) = \sum_{i=1}^n \left[c_i \log
h_i + c_ix_i'\theta - \exp(x_i'\theta) H_i\right]
$$

where $$h_i=h_0(t_i)$$ and $$H_i = \sum_{j=1}^i h_j$$. Thus the score function is

$$
\nabla \ell(\theta; \mathbf{t},\mathbf{c},\mathbf{x}) = \sum_{i=1}^n\left[c_i x_i - H_i \exp(x_i'\theta) x_i\right] =
\sum_{i=1}^n (c_i - H_i \exp(x_i'\theta)) x_i,
$$

Note that the baseline hazard evaluations $$h_i$$ which form $$H_i$$ are unknown.  However, one can show that their MLE given $$\theta$$ is

$$
\hat{h_i}=  \frac{c_i}{\sum_{j \in \mathcal{R}_i}\exp(x_j'\theta)}
$$

where $$\mathcal{R}_i$$ is the _risk set_, i.e., the set of units available to fail. In this case, $$\mathcal{R}_i = \{i, i+1, \ldots n\}$$ by our ordering assumption.

One can also derive the Hessian, but a simple maximum likelihood procedure using gradient descent can now easily proceed: one iterates $$\theta_n$$, re-estimates $$\hat h_i$$'s conditional on the new update of $$\theta_n$$, then repeats.

Perhaps surprisingly, estimates from this partial likelihood, which require no estimation of the baseline hazard $$h_0(\cdot)$$, share the same asymptotic properties as that of the full likelihood (Tsiatis, 1975).

## Poisson models and stochastic processes
On a modelling perspective, the Cox proportional hazards model has a striking resemblance to Poisson regression. If the baseline hazard is a constant $$h_0(t)=\lambda$$ for some $$\lambda\in\mathbb{R}$$, then the log likelihood is

```
\begin{align}
\ell(\theta; \mathbf{t},\mathbf{c},\mathbf{x})
&= \sum_{i=1}^n \left[c_i \log
h(t_i\mid\theta,x_i) + \log S(t_i\mid\theta,x_i)\right]
\\
&= \sum_{i=1}^n \left[c_i \log
[\lambda\exp(x_i'\theta)] - t_i\lambda\exp(x_i'\theta)\right]
\end{align}
```

Excluding additive constants, this is the same expression as the log likelihood of the $$c_i$$'s, seen as realizations $$c_i\mid t_i,x_i\sim\mathrm{Poisson}(\mu_i)$$, where $$\mu_i=t_i\lambda\exp(x_i'\theta)$$. Therefore the Cox proportional hazards model with constant baseline $$\lambda$$ is equivalent to the Poisson regression

$$
\log\mathbb{E}[c_i\mid t_i,x_i] =
\log t_i + \log\lambda + x_i'\theta
$$

where one can interpret $$\log t_i$$ as an offset for modelling the rate $$\log (\mathbb{E}[c_i\mid t_i,x_i]/t_i)$$, and $$\log\lambda$$ as an intercept. In full generality, the Cox model is simply a Poisson model for rates in which the "intercept" $$\log h_0(t_i)$$ is a function varying over time.

If one has prior knowledge about the structural form  of the baseline hazard, then it makes sense to forgo the partial likelihood approach and to adopt a Bayesian methodology, in which one places a stochastic process as a prior for $$h_0(t)$$, e.g., using a Gaussian process, Beta process, or extended Gamma process, see Dey and Rao (2005; Section 3.2.1) for more details.

The specification of the hazard function in survival analysis is analogous to specifying the intensity function for point processes. That is, as the Cox proportional hazards model with constant baseline hazard $$\lambda$$ is equivalent to a form of Poisson regression, a Cox process with constant intensity $$\lambda$$ is equivalent to the Poisson process.

Another interesting relationship is that in survival analysis, the estimand of interest is often not a count but a rate. After all, a count of 1 already implies that the person is dead! There are more general formulations of survival analysis which consider multiple failures for a single unit, however, and the special case of just one already has much significant analysis. How this might tie into current progress on Bayesian nonparametrics for count data is unknown and certainly worth looking into.

### References
* David R. Cox. Regression models and life tables (with discussion). _Journal of the Royal Statistical Society: Series B_, 34:187-220, 1972.
* David R. Cox. Partial likelihood. _Biometrika_, 62: 269-276, 1975.
* David R. Cox and David Oakes. _Analysis of Survival Data_. CRC Press, 1984.
* Dipak Dey and Calyampudi Radhakrishna Rao. _Bayesian Thinking: Modeling and Computation_. North Holland, 2005.
* David A. Freedman. Survival analysis: a primer. Technical Report, 2008.
* Anastasios Tsiatis. A Large Sample Study of Cox's Regression Model. _Annals of Statistics_, 9(1): 93-108, 1981.
