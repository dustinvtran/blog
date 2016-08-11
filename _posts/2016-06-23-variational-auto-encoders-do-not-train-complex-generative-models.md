---
layout: post
title: Variational auto-encoders do not train complex generative models
---

There is a [tutorial on variational auto-encoders](http://arxiv.org/abs/1606.05908) which popped up on my arXiv radar this week. Thanks to Carl Doersch for writing this tutorial! As a researcher in this area, I believe there is sorely a need for an exposition of recent developments in variational inference. Variational inference's application for enabling deep generative models has exploded in the past few years. This tutorial is a great step in that direction. Inspired by this and  discussions with others during ICML, I'd like to make a few comments to clarify misunderstandings that are pervasive among newcomers to variational inference, Bayesian analysis, and/or auto-encoders.

Variational auto-encoders are _not_ a way to train generative models. The generative model is _part_ of the variational auto-encoder, which is typically a deep latent Gaussian model (Rezende et al., 2014). Training generative models is done by inference, typically variational inference (Hinton and Van Camp, 1993; Waterhouse et al., 1996) with an inference network. This model-inference combination is what defines a variational auto-encoder (Kingma and Welling, 2014) and more classically a Helmholtz machine (Dayan et al., 1995). Making precise the separate components of model and inference is crucial for extensibility. For example, how can we build on the inference, leveraging Monte Carlo methods (MacKay and Gibbs, 1999; Salimans et al., 2015), or build on the modeling, leveraging recurrent compositions (Gregor et al., 2015)? If I learned anything at my time at Harvard statistics with people like Don Rubin, I learned that being precise about our language prevents our discourse from being ambiguous and our terms from becoming overloaded.

Generative models are usually motivated from their immediate application of generating data that look like the training data. Variational auto-encoders, like, say, generative adversarial networks, are most generally a way to both postulate and infer complex data generative processes. This is possibly for unsupervised tasks, possibly supervised tasks, semi-supervised tasks, or even causal inferences. They are part of an underlying statistical methodology.<sup>1</sup> This methodology simply frames models as distributions over data and latent variables, allowing models to address a broad array of downstream tasks with any size of data. For example, deep generative models can achieve "state-of-the-art" for supervised learning on small amounts of data (Damianou and Lawrence, 2013) and generalization with few examples (Salakhutdhinov et al., 2013).

The neural network used in the encoder (variational distribution) does not lead to any richer approximating distribution. It is a way to amortize inference such that the number of parameters does not grow with the size of the data (an incredible feat, but not one for expressivity!) (Stuhlmüller et al., 2013). This is better explained from the perspective of variational inference than auto-encoders. For example, suppose we have a model with latent variables that are Gaussian a priori (as in VAEs). We may choose a fully factorized Gaussian as the variational distribution, where each latent variable is rendered independent. The inference network takes data as input and outputs the local variational parameters relevant to each data point. The optimal inference network outputs the set of Gaussian parameters which maximizes the variational objective. This means a variational auto-encoder with a perfect inference network can only do as well as a fully factorized Gaussian with no inference network.<sup>2</sup>

<sup>1</sup> The underlying statistical methodology of latent variable models is typically Bayesian. However, the distinction between frequentist and Bayesian models is practically none, e.g., Harville (1977).

<sup>2</sup> Much recent work, including my own, has tried to improve this simple approximation to improve our approximate inference. I invite you to the incredible developments since the past year: Rezende and Mohamed, 2015; Salimans et al., 2015; Tran et al., 2015; Tran et al., 2016; Burda et al., 2016; Ranganath et al., 2016; Maaløe et al., 2016; Mnih and Rezende, 2016; Louizos et al., 2016; Johnson et al., 2016; Dinh et al., 2016; Kingma et al., 2016.

## References

+ Burda, Y., Grosse, R., & Salakhutdinov, R. (2016). Importance Weighted Autoencoders. In _International Conference on Learning Representations._
+ Damianou, A. C., & Lawrence, N. D. (2013). Deep Gaussian Processes. In _Artificial Intelligence and Statistics._
+ Dayan, P., Hinton, G. E., Neal, R. M., & Zemel, R. S. (1995). The Helmholtz Machine. Neural Computation, 7(5), 889–904. http://doi.org/10.1162/neco.1995.7.5.889
+ Dinh, L., Sohl-Dickstein, J., & Bengio, S. (2016). Density estimation using Real NVP. _arXiv.org._
+ Harville, D. A (1977). Maximum likelihood approaches to variance component estimation and to related problems. _Journal of the American Statistical Association_, 72(358):320–338.
+ Hinton, G. and Van Camp, D (1993). Keeping the neural networks simple by minimizing the description length of the weights. In _Computational Learning Theory_, pp. 5–13. ACM.
+ Johnson, M. J., Duvenaud, D., Wiltschko, A. B., Datta, S. R., & Adams, R. P. (2016). Composing graphical models with neural networks for structured representations and fast inference. _arXiv.org_.
+ Kingma, D. P., & Welling, M. (2014). Auto-Encoding Variational Bayes. In _International Conference on Learning Representations_.
+ Kingma, D. P., Salimans, T., & Welling, M. (2016). Improving Variational Inference with Inverse Autoregressive Flow. _arXiv.org_.
+ Louizos, C., & Welling, M. (2016). Structured and Efficient Variational Deep Learning with Matrix Gaussian Posteriors. In _International Conference on Machine Learning_.
+ Maaløe, L., Sønderby, C. K., Sønderby, S. K., & Winther, O. (2016). Auxiliary Deep Generative Models. In _International Conference on Machine Learning_.
+ MacKay, D. J., & Gibbs, M. N. (1999). Density networks. _Statistics and neural networks: advances at the interface_. Oxford University Press, Oxford, 129-144.
+ Mnih, A., & Rezende, D. J. (2016). Variational inference for Monte Carlo objectives. In _International Conference on Machine Learning_.
+ Ranganath, R., Tran, D., & Blei, D. M. (2016). Hierarchical Variational Models. In _International Conference on Machine Learning_.
+ Rezende, D. J., Mohamed, S., & Wierstra, D. (2014). Stochastic Backpropagation and Approximate Inference in Deep Generative Models. In _International Conference on Machine Learning_.
+ Salimans, T., Kingma, D. P., & Welling, M. (2015). Markov Chain Monte Carlo and Variational Inference: Bridging the Gap. In _International Conference on Machine Learning_.
+ Salakhutdinov, R., Tenenbaum, J. B., and Torralba, A (2013). Learning with hierarchical-deep models. _Pattern Analysis and Machine Intelligence, IEEE Transactions on_, 35 (8):1958–1971.
+ Stuhlmüller, A., Taylor, J., & Goodman, N. (2013). Learning Stochastic Inverses. In _Neural Information Processing Systems_.
+ Tran, D., Blei, D. M., & Airoldi, E. M. (2015). Copula variational inference. In _Neural Information Processing Systems_.
+ Tran, D., Ranganath, R., & Blei, D. M. (2016). The Variational Gaussian Process. _International Conference on Learning Representations_.
+ Waterhouse, S., MacKay, D., and Robinson, T (1996). Bayesian methods for mixtures of experts. In _Neural Information Processing Systems_.
