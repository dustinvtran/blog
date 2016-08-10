---
layout: post
title: Comparing elastic net to stochastic gradient descent for GLMs
---

The elastic net [3] provides a regularized objective function that meets a compromise between the two extremes of Lasso [2] and ridge regression. It takes into account both the Bayesian properties of ridge regression and also the need for sparse parameters via the $\ell\_1$ penalty from Lasso.

Let $\\{(x\_i,y\_i):~i=1,\ldots,N\\}$ be our observed data set where the responses $y\_i\in\mathbb{R}$ and the predictors $x\_i\in\mathbb{R}^p$. Simply put, elastic net regularization is defined by solving
$$
\min\_{(\beta\_0,\beta)\in\mathbb{R}^{p+1}} R\_\lambda(\beta\_0, \beta)
= \min\_{(\beta\_0,\beta)\in\mathbb{R}^{p+1}} \left[
\frac{1}{2N}
\sum\_{i=1}^N (y\_i - \beta\_0 - x\_i^T\beta)^2
+ \lambda P\_\alpha(\beta)
\right],
$$
where the penalty function is defined as
$$
P\_\alpha(\beta) = (1-\alpha)\frac{1}{2}\|\beta\|\_{\ell\_2}^2 + \alpha\|\beta\|\_{\ell\_1}.
$$
This was applied to generalized linear models (GLMs) by Tibshirani, and also encoded as an R package for optimized calculations [1]. In particular, one can take advantage of the many sparse entries underlying the computation, as the dimension of the corresponding matrices written in sparse column format is effectively smaller; this leads to reduced runtimes of $O(pm)$ as the inner product operations sum only over the nonzero terms ($m$ being the number of nonzero terms, $p$ the dimension of the predictors).

In one example, Friedman et. al [1] effectively show that this method to compute elastic nets is more efficient and more accurate than LARS, another regression algorithm meant for high-dimensional problems. Statisticians tend to keep to their own methods for computation, so let's try something different; let's see what happens when under the same example, we run standard stochastic gradient descent (SGD) and its implicit variant as a comparison.

We simulate data according to the example offered in the paper by Friedman et. al [1]: generate $N$ Gaussian observations with $p$ predictors, where each predictor pair $(X\_j,~X\_{j'})$ has the same population correlation $\rho$. Range $\rho$ from $0$ to $0.95$, and generate outcomes
$$
Y = \sum\_{j=1}^p X\_j\beta\_j + k\cdot Z,
$$
where $\beta\_j=(-1)^j\exp(-2(j-1)/20)$, $Z\sim N(0,1)$, and $k$ is chosen so that the signal-to-noise ratio is $3.0$.

We use the R package `glmnet` in order to emulate the times displayed on Table 1 of the paper, with both the "naive" and "covariance" variants of the algorithm. As for SGD and implicit SGD, let $b^2=\rho/(1-\rho)$, so then the covariance matrix can be rewritten as
$$\mathbb{E}[xx^T] = b^2U+I.$$
Then for computing standard SGD, we shall use the learning rate
$$
\alpha\_n = \frac{\alpha}{\frac{\alpha}{\gamma\_0} +n},
$$
where $\gamma\_0=1/\operatorname{trace}(A) = 1/((1+b^2)p)$ and $\alpha$ is the minimal eigenvalue  $\lambda\_0=1$. (The latter is true because all eigenvalues are 1 excluding one which is $\lambda=1+pb^2$. One can prove this easily by $(b^2U+I)x=\lambda x\iff (1-\lambda)x = -b^2 S\cdot 1$, where $S=\sum x\_i$.) As for implicit SGD, we'll just use $\alpha\_n=\alpha/n$.

The following is run on a 2.6 Ghz i5 core processor.

## Runtime (s)
<table border="1" style="width:100%; text-align:center;">
<thead>
  <tr>
    <th>dimension space</th>
    <th>method</th>
    <th>$\rho=0$</th>
    <th>$\rho=0.10$</th>
    <th>$\rho=0.20$</th>
    <th>$\rho=0.50$</th>
    <th>$\rho=0.90$</th>
    <th>$\rho=0.95$</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>$N=1000,~p=100$</td>
    <td>covariance (glmnet)</td>
    <td>$0.011$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.011$</td>
    <td>$0.015$</td>
    <td>$0.017$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.032$</td>
    <td>$0.033$</td>
    <td>$0.037$</td>
    <td>$0.048$</td>
    <td>$0.134$</td>
    <td>$0.177$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.020$</td>
    <td>$0.021$</td>
    <td>$0.021$</td>
    <td>$0.023$</td>
    <td>$0.019$</td>
    <td>$0.020$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.020$</td>
    <td>$0.020$</td>
    <td>$0.019$</td>
    <td>$0.019$</td>
    <td>$0.019$</td>
    <td>$0.018$</td>
  </tr>
  <tr></tr>
  <tr>
    <td>$N=5000,~p=100$</td>
    <td>covariance (glmnet)</td>
    <td>$0.035$</td>
    <td>$0.033$</td>
    <td>$0.034$</td>
    <td>$0.037$</td>
    <td>$0.039$</td>
    <td>$0.040$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.119$</td>
    <td>$0.121$</td>
    <td>$0.139$</td>
    <td>$0.179$</td>
    <td>$0.490$</td>
    <td>$0.834$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.118$</td>
    <td>$0.124$</td>
    <td>$0.113$</td>
    <td>$0.112$</td>
    <td>$0.113$</td>
    <td>$0.112$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.105$</td>
    <td>$0.104$</td>
    <td>$0.105$</td>
    <td>$0.104$</td>
    <td>$0.105$</td>
    <td>$0.104$</td>
  </tr>
  <tr></tr>
  <tr>
    <td>$N=100,~p=1000$</td>
    <td>covariance (glmnet)</td>
    <td>$0.041$</td>
    <td>$0.041$</td>
    <td>$0.045$</td>
    <td>$0.061$</td>
    <td>$0.098$</td>
    <td>-</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.023$</td>
    <td>$0.020$</td>
    <td>$0.025$</td>
    <td>$0.032$</td>
    <td>$0.046$</td>
    <td>-</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.007$</td>
    <td>$0.007$</td>
    <td>$0.006$</td>
    <td>$0.008$</td>
    <td>$0.006$</td>
    <td>-</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.007$</td>
    <td>$0.007$</td>
    <td>$0.006$</td>
    <td>$0.006$</td>
    <td>$0.006$</td>
    <td>-</td>
  </tr>
  <tr></tr>
  <tr>
    <td>$N=100,~p=5000$</td>
    <td>covariance (glmnet)</td>
    <td>$0.204$</td>
    <td>$0.238$</td>
    <td>$0.243$</td>
    <td>$0.314$</td>
    <td>$0.517$</td>
    <td>$0.615$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.075$</td>
    <td>$0.093$</td>
    <td>$0.071$</td>
    <td>$0.082$</td>
    <td>$0.098$</td>
    <td>$0.145$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.031$</td>
    <td>$0.031$</td>
    <td>$0.032$</td>
    <td>$0.031$</td>
    <td>$0.029$</td>
    <td>$0.033$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.029$</td>
    <td>$0.028$</td>
    <td>$0.029$</td>
    <td>$0.028$</td>
    <td>$0.028$</td>
    <td>$0.030$</td>
  </tr>
  <tr></tr>
  <tr>
    <td>$N=100,~p=20000$</td>
    <td>covariance (glmnet)</td>
    <td>$0.923$</td>
    <td>$0.906$</td>
    <td>$0.955$</td>
    <td>$1.313$</td>
    <td>$2.082$</td>
    <td>$2.927$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.381$</td>
    <td>$0.377$</td>
    <td>$0.376$</td>
    <td>$0.390$</td>
    <td>$0.405$</td>
    <td>$0.736$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.152$</td>
    <td>$0.147$</td>
    <td>$0.148$</td>
    <td>$0.156$</td>
    <td>$0.147$</td>
    <td>$0.153$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.140$</td>
    <td>$0.170$</td>
    <td>$0.146$</td>
    <td>$0.143$</td>
    <td>$0.137$</td>
    <td>$0.145$</td>
  </tr>
  <tr></tr>
  <tr>
    <td>$N=100,~p=50000$</td>
    <td>covariance (glmnet)</td>
    <td>$2.280$</td>
    <td>$2.321$</td>
    <td>$2.322$</td>
    <td>$2.693$</td>
    <td>$4.901$</td>
    <td>$6.176$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.745$</td>
    <td>$0.770$</td>
    <td>$0.753$</td>
    <td>$0.775$</td>
    <td>$0.996$</td>
    <td>$1.735$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.421$</td>
    <td>$0.392$</td>
    <td>$0.379$</td>
    <td>$0.383$</td>
    <td>$0.390$</td>
    <td>$0.393$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.363$</td>
    <td>$0.370$</td>
    <td>$0.366$</td>
    <td>$0.374$</td>
    <td>$0.368$</td>
    <td>$0.370$</td>
  </tr>
</tbody>
</table>

## Mean Squared Error
<table border="1" style="width:100%; text-align:center;">
<thead>
  <tr>
    <th>dimension space</th>
    <th>method</th>
    <th>$\rho=0$</th>
    <th>$\rho=0.10$</th>
    <th>$\rho=0.20$</th>
    <th>$\rho=0.50$</th>
    <th>$\rho=0.90$</th>
    <th>$\rho=0.95$</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>$N=1000,~p=100$</td>
    <td>covariance (glmnet)</td>
    <td>$0.045$</td>
    <td>$0.045$</td>
    <td>$0.043$</td>
    <td>$0.048$</td>
    <td>$0.054$</td>
    <td>$0.075$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.045$</td>
    <td>$0.045$</td>
    <td>$0.043$</td>
    <td>$0.048$</td>
    <td>$0.054$</td>
    <td>$0.075$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.054$</td>
    <td>$0.057$</td>
    <td>$0.059$</td>
    <td>$0.062$</td>
    <td>$0.209$</td>
    <td>$0.228$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.054$</td>
    <td>$0.057$</td>
    <td>$0.058$</td>
    <td>$0.057$</td>
    <td>$0.091$</td>
    <td>$0.114$</td>
  </tr>
  <tr></tr>
  <tr>
    <td>$N=5000,~p=100$</td>
    <td>covariance (glmnet)</td>
    <td>$0.026$</td>
    <td>$0.028$</td>
    <td>$0.028$</td>
    <td>$0.031$</td>
    <td>$0.051$</td>
    <td>$0.072$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.026$</td>
    <td>$0.028$</td>
    <td>$0.028$</td>
    <td>$0.031$</td>
    <td>$0.053$</td>
    <td>$0.072$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.020$</td>
    <td>$0.020$</td>
    <td>$0.021$</td>
    <td>$0.022$</td>
    <td>$0.146$</td>
    <td>$0.206$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.020$</td>
    <td>$0.020$</td>
    <td>$0.021$</td>
    <td>$0.022$</td>
    <td>$0.036$</td>
    <td>$0.046$</td>
  </tr>
  <tr></tr>
  <tr>
    <td>$N=100,~p=1000$</td>
    <td>covariance (glmnet)</td>
    <td>$0.063$</td>
    <td>$0.060$</td>
    <td>$0.061$</td>
    <td>$0.057$</td>
    <td>$0.066$</td>
    <td>$0.071$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.063$</td>
    <td>$0.060$</td>
    <td>$0.061$</td>
    <td>$0.057$</td>
    <td>$0.066$</td>
    <td>$0.071$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.071$</td>
    <td>$0.072$</td>
    <td>$0.072$</td>
    <td>$0.073$</td>
    <td>$0.074$</td>
    <td>$0.074$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.071$</td>
    <td>$0.071$</td>
    <td>$0.071$</td>
    <td>$0.072$</td>
    <td>$0.073$</td>
    <td>$0.073$</td>
  </tr>
  <tr></tr>
  <tr>
    <td>$N=100,~p=5000$</td>
    <td>covariance (glmnet)</td>
    <td>$0.030$</td>
    <td>$0.031$</td>
    <td>$0.031$</td>
    <td>$0.033$</td>
    <td>$0.031$</td>
    <td>$0.033$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.030$</td>
    <td>$0.031$</td>
    <td>$0.031$</td>
    <td>$0.033$</td>
    <td>$0.031$</td>
    <td>$0.033$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.033$</td>
    <td>$0.032$</td>
    <td>$0.033$</td>
    <td>$0.033$</td>
    <td>$0.033$</td>
    <td>$0.033$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.033$</td>
    <td>$0.032$</td>
    <td>$0.033$</td>
    <td>$0.033$</td>
    <td>$0.033$</td>
    <td>$0.033$</td>
  </tr>
  <tr></tr>
  <tr>
    <td>$N=100,~p=20000$</td>
    <td>covariance (glmnet)</td>
    <td>$0.016$</td>
    <td>$0.015$</td>
    <td>$0.015$</td>
    <td>$0.015$</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.016$</td>
    <td>$0.015$</td>
    <td>$0.015$</td>
    <td>$0.015$</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
    <td>$0.016$</td>
  </tr>
  <tr></tr>
  <tr>
    <td>$N=100,~p=50000$</td>
    <td>covariance (glmnet)</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
  </tr>
  <tr>
    <td></td>
    <td>naive (glmnet)</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
  </tr>
  <tr>
    <td></td>
    <td>SGD</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
  </tr>
  <tr>
    <td></td>
    <td>implicit SGD</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
    <td>$0.010$</td>
  </tr>
</tbody>
</table>

<br>

Stochastic gradient descent is obviously faster for large $p$. In particular, it achieves much faster performance after the $p\geq 1000$ cases, and with competitive mean squared error if $N$ or $P$ is large ($N$ or $p\geq 1000$). Empirically speaking, it's quite safe to conclude that stochastic gradient descent is the better scalable algorithm. Moreover, SGD's error is independent of the correlation, so while elastic net is faster for small $N$ or $p$, we still recommend using SGD in the case of highly correlated variables and small $N$ or $p$. The better estimate provided by SGD may be worth more than the slightly slower runtime.

We can also run the same simulation varying $N$ and $p$ with averaged SGD (ASGD), which takes roughly the same time as standard SGD but with an improved estimate. The results would essentially be the same, only that the cutoff for a better estimate for ASGD over elastic net would be at a smaller $N$ than standard SGD's.

The takeaway message? SGD is awesome.

[1] J. Friedman, T. Hastie, and R. Tibshirani. Regularization paths for generalized linear
models via coordinate descent. _Journal of statistical software_, 33(1):1, 2010.

[2] R. Tibshirani. Regression Shrinkage and Selection via the Lasso. _Journal of the Royal
Statistical Society B_, 58:267–288, 1996.

[3] H. Zou H, T. Hastie. Regularization and Variable Selection via the Elastic Net. _Journal
of the Royal Statistical Society B_, 67(2):301–320, 2005.
